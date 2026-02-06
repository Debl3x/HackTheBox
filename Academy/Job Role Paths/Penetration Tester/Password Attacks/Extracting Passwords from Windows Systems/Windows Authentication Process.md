# Windows Authentication Process

## Overview

The Windows authentication process involves several modules responsible for logon, retrieval, and verification of credentials. Among the authentication mechanisms:
- **Kerberos**: one of the most used and complex
- **LSA (Local Security Authority)**: a protected subsystem that authenticates users, manages local logons, and translates user names into SIDs

The security subsystem maintains security policies and user accounts. On a Domain Controller, these policies apply across the domain and are stored in Active Directory.

## Main Components

### WinLogon
Trusted system process responsible for:
- Launching **LogonUI** to request credentials
- Managing password changes
- Locking/unlocking the workstation

WinLogon intercepts logon input from the keyboard (via RPC from Win32k.sys) and passes credentials to **LSASS** for authentication.

### LSASS (Local Security Authority Subsystem Service)
- **Location**: `%SystemRoot%\System32\Lsass.exe`
- **Role**: Gatekeeper of the Windows system that handles all authentication processes
- **Responsibilities**: Enforces local security policy, authenticates users, and forwards audit logs

#### LSASS Authentication Packages

| Package | Description |
|---------|-------------|
| **Lsasrv.dll** | LSA Server service - manages security policies and selects NTLM or Kerberos |
| **Msv1_0.dll** | Authentication for local logons without custom authentication |
| **Samsrv.dll** | Security Accounts Manager - stores local security accounts |
| **Kerberos.dll** | Security package for Kerberos authentication |
| **Netlogon.dll** | Network logon service |
| **Ntdsa.dll** | Directory System Agent - manages the AD database (ntds.dit), only on DCs |

## Credential Storage

### SAM Database
- **Location**: `%SystemRoot%\system32\config\SAM` (mounted under `HKLM\SAM`)
- **Content**: User account credentials (LM or NTLM hashes)
- **Access**: Requires SYSTEM privileges
- **Protection**: SYSKEY (since Windows NT 4.0) partially encrypts the SAM file

**Operation mode**:
- **Workgroup**: Local SAM stores all users
- **Domain**: The Domain Controller validates via Active Directory (ntds.dit)

### Credential Manager
Built-in feature for storing and managing credentials for:
- Network resources
- Websites
- Applications

**Storage location**:
```powershell
PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\
```

Credentials are encrypted in the Credential Locker of each user profile.

### NTDS (Active Directory Database)
- **File**: `NTDS.dit`
- **Synchronization**: Between all DCs (except RODCs)
- **Content**:
  - User accounts (username & password hash)
  - Group accounts
  - Computer accounts
  - Group Policy Objects (GPOs)

---

## Summary
The Windows authentication process relies on a modular architecture with **WinLogon** as the entry point, **LSASS** as the central manager, and different credential storage systems (**SAM**, **NTDS.dit**, **Credential Manager**) depending on the context (local/domain).