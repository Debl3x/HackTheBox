# Spraying, Stuffing & Defaults

## Password Spraying

Password spraying is a brute-force attack where an attacker uses a single password across many user accounts. It's effective when default credentials are common (e.g., `ChangeMe123!`).

**Common Tools:**
- **Web apps**: Burp Suite
- **Active Directory**: NetExec, Kerbrute

```bash
netexec smb 10.100.38.0/24 -u usernames.list -p 'ChangeMe123!'
```

## Credential Stuffing

Credential stuffing uses stolen credentials from one service to access others. Since users often reuse passwords across platforms, this attack frequently succeeds.

```bash
hydra -C user_pass.list ssh://10.100.38.23
```

## Default Credentials

Many systems (routers, firewalls, databases) ship with default credentials. While best practice requires changing them, oversights happen.

### Finding Default Credentials

Use the **Default Credentials Cheat Sheet** tool:

```bash
pip3 install defaultcreds-cheat-sheet
creds search linksys
```

### Common Router Defaults

| Brand | Default IP | Username | Password |
|-------|-----------|----------|----------|
| 3Com | 192.168.1.1 | admin | Admin |
| Belkin | 192.168.2.1 | admin | admin |
| D-Link | 192.168.0.1 | admin | Admin |
| Linksys | 192.168.1.1 | admin | Admin |
| Netgear | 192.168.0.1 | admin | password |
