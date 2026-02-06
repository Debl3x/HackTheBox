# Credential Hunting in Windows

Once access to a Windows machine is obtained (GUI or CLI), **credential hunting** becomes a high-value activity. Credential hunting consists of systematically searching the file system, memory, and applications to uncover stored credentials.

## Scenario
We have gained access to an **IT administrator’s Windows 10 workstation** via RDP. Because of the admin’s role, this system is likely to contain sensitive credentials used for infrastructure management.

---

## 1. Search-Centric Approach

Modern operating systems and applications are heavily search-driven. This can be leveraged to efficiently locate credentials that users may have stored insecurely.

To optimize searches, always consider **how the target system is used**.

### Example: IT Admin Daily Activities
An IT administrator may:
- Manage servers and network devices
- Use RDP, SSH, VPNs
- Maintain scripts and configuration files
- Access databases and internal services
- Use password managers or browser-stored credentials

Each of these activities may involve stored credentials.

---

## 2. Key Terms to Search For

When searching files or application data, the following keywords are commonly associated with credentials:

- password
- passphrase
- key
- keys
- username
- user
- user account
- creds
- credentials
- passkeys
- login
- configuration
- dbcredential
- dbpassword
- pwd

These keywords should be used strategically rather than randomly.

---

## 3. Windows Search (GUI)

If GUI access is available:
- Use **Windows Search** to locate files and settings containing credential-related terms.
- By default, Windows Search scans:
  - OS settings
  - Indexed files
  - Common file locations

This can quickly surface plaintext files, scripts, or configuration data containing sensitive information.

---

## 4. LaZagne

**LaZagne** is a credential recovery tool that extracts credentials stored by various applications.

### Common LaZagne Modules

| Module | Description |
|------|------------|
| browsers | Extracts credentials from Chromium, Firefox, Edge, Opera |
| chats | Extracts credentials from chat applications (e.g. Skype) |
| mails | Searches mail clients (Outlook, Thunderbird) |
| memory | Dumps credentials from memory (KeePass, LSASS) |
| sysadmin | Extracts credentials from tools like WinSCP and OpenVPN |
| windows | Extracts Windows credentials (LSA secrets, Credential Manager) |
| wifi | Dumps saved WiFi credentials |

> Web browsers are a prime target because many users rely on built-in password managers. Although stored credentials are encrypted, many tools exist to decrypt them. LaZagne supports over 35 browsers on Windows.

---

## 5. Using LaZagne

Transfer `LaZagne.exe` to the target (e.g. via clipboard using RDP).

Execute from Command Prompt or PowerShell:

```cmd
start LaZagne.exe all
````

Optional verbose mode:

```cmd
start LaZagne.exe all -vv
```

### Example Output

```
[+] Password found !!!
URL: 10.129.202.51
Login: admin
Password: SteveisReallyCool123
Port: 22
```

This demonstrates how easily credentials can be recovered when applications store them insecurely.

---

## 6. Searching Files with findstr

`findstr` allows pattern-based searching across multiple file types.

Example command:

```cmd
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

Options:

* `/S` : Search recursively
* `/I` : Case-insensitive
* `/M` : Print filenames only

This is especially effective against scripts, configuration files, and documentation.

---

## 7. Additional Credential Storage Locations

When hunting credentials, also consider:

* Group Policy Preferences passwords in **SYSVOL**
* Scripts stored in **SYSVOL**
* Scripts on IT or admin file shares
* `web.config` files on development or application servers
* `unattend.xml` files
* Credentials in AD user or computer **description fields**
* KeePass databases (if master password can be guessed or cracked)
* User systems and shared folders
* Files named:

  * `pass.txt`
  * `passwords.docx`
  * `passwords.xlsx`
* SharePoint document libraries

---

## 8. Key Takeaways

* Credential hunting should be **targeted**, not random
* Understanding the **role of the system owner** is critical
* Admin workstations are high-value targets
* Many applications store credentials insecurely
* Simple tools can yield domain-wide impact

Effective credential hunting often leads directly to privilege escalation or lateral movement.
