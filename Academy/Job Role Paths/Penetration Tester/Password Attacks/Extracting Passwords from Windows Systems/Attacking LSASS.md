# Attacking LSASS

## Objective

In addition to dumping the **SAM database**, targeting **LSASS** allows attackers to extract **credentials stored in memory**, such as password hashes, Kerberos tickets, and DPAPI keys. LSASS is responsible for authentication, security policy enforcement, and credential handling on Windows systems.

---

## Why Dump LSASS

* LSASS stores credentials for **active logon sessions**
* Dumping its memory allows **offline credential extraction**
* Offline analysis is faster, stealthier, and reduces time spent on the target

---

## Methods to Dump LSASS Memory

### 1. Task Manager (GUI)

Requires an interactive graphical session.
Steps:

* Open Task Manager
* Locate **Local Security Authority Process (lsass.exe)**
* Right-click → **Create dump file**
* Dump is saved as `lsass.dmp` in `%TEMP%`

Limitation: requires GUI access.

---

### 2. Rundll32 + comsvcs.dll (CLI)

Works from command line with administrative privileges.

Steps:

1. Identify LSASS PID

   * CMD: `tasklist /svc`
   * PowerShell: `Get-Process lsass`

2. Dump LSASS memory:

```
rundll32 C:\Windows\System32\comsvcs.dll, MiniDump <PID> C:\lsass.dmp full
```

Notes:

* Faster and more flexible than Task Manager
* Commonly detected and blocked by modern antivirus solutions

---

## Transferring the Dump

The generated `lsass.dmp` file must be transferred to the attack host using any available file transfer technique.

---

## Extracting Credentials with Pypykatz

**Pypykatz** is a Python implementation of Mimikatz and can parse LSASS dumps on Linux systems.

Command:

```
pypykatz lsa minidump lsass.dmp
```

Pypykatz extracts credentials captured at the time of the dump.

---

## Key Credential Types Extracted

### MSV

* Username, domain, SID
* NT and SHA1 password hashes
* Used for NTLM authentication
* Hashes can be cracked offline

### WDIGEST

* May contain **clear-text passwords**
* Enabled by default on older Windows versions
* Disabled by default on modern systems

### Kerberos

* Usernames, tickets, encryption keys
* Can be reused to access domain resources

### DPAPI

* Master keys for decrypting stored secrets
* Can lead to browser, Wi-Fi, and application credentials

---

## Cracking NT Hashes

Extracted NT hashes can be cracked offline using Hashcat.

Example:

```
hashcat -m 1000 <NT_HASH> rockyou.txt
```

If successful, the plaintext password is recovered.

---

## Key Takeaway

Dumping LSASS is a powerful post-exploitation technique that can yield high-value credentials from memory. When combined with offline analysis tools like Pypykatz and Hashcat, it enables effective credential harvesting with minimal interaction with the target system.