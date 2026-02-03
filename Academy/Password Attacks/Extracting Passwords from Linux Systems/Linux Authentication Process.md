# Linux Authentication Process

Linux-based distributions support multiple authentication mechanisms. One of the most commonly used is **Pluggable Authentication Modules (PAM)**.

PAM modules such as `pam_unix.so` or `pam_unix2.so` are typically located on Debian-based systems in:

```
/usr/lib/x86_64-linux-gnu/security/
```

These modules handle:

* User authentication
* Account management
* Session management
* Password changes

For example, when a user changes their password using the `passwd` command, PAM is invoked to safely process and store the new credentials.

The `pam_unix.so` module uses standardized system library APIs to update account information. The primary files involved are:

* `/etc/passwd`
* `/etc/shadow`

PAM also supports many additional modules, including LDAP, mount operations, and Kerberos authentication.

---

## `/etc/passwd` File

The `/etc/passwd` file contains information about every user on the system and is **world-readable**. Each line corresponds to a single user and consists of **seven colon-separated fields**.

Example entry:

```text
htb-student:x:1000:1000:,,,:/home/htb-student:/bin/bash
```

| Field          | Description        | Value             |
| -------------- | ------------------ | ----------------- |
| Username       | Login name         | htb-student       |
| Password       | Password indicator | x                 |
| User ID        | UID                | 1000              |
| Group ID       | GID                | 1000              |
| GECOS          | User info          | ,,,               |
| Home directory | Home path          | /home/htb-student |
| Default shell  | Login shell        | /bin/bash         |

### Password Field Behavior

* On modern systems, the password field usually contains `x`, indicating that the password hash is stored in `/etc/shadow`
* On very old systems, password hashes may appear directly in `/etc/passwd`
* If hashes are stored here, attackers can attempt offline cracking since the file is world-readable

### Misconfiguration Risk

If `/etc/passwd` is accidentally writable, an attacker could remove the password requirement entirely.

Example:

```text
root::0:0:root:/root:/bin/bash
```

Result:

```bash
$ su
# root access without password
```

This scenario is rare but possible due to administrator misconfiguration, especially when improper permissions are applied to system directories like `/etc`.

---

## `/etc/shadow` File

To mitigate the risks of exposing password hashes, Linux uses the `/etc/shadow` file.

Key characteristics:

* Stores all password hashes
* Only readable by privileged users
* Users without a shadow entry are considered invalid

Example entry:

```text
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```

| Field             | Description             |
| ----------------- | ----------------------- |
| Username          | Account name            |
| Password          | Hashed password         |
| Last change       | Days since epoch        |
| Min age           | Minimum password age    |
| Max age           | Maximum password age    |
| Warning period    | Password expiry warning |
| Inactivity period | Account inactivity      |
| Expiration date   | Account expiration      |
| Reserved          | Unused                  |

### Password Field Values

* `!` or `*` → Password login disabled
* Empty field → No password required
* Other authentication methods (SSH keys, Kerberos) may still work

### Hash Format

```text
$<id>$<salt>$<hash>
```

#### Common Hash Identifiers

| ID   | Algorithm     |
| ---- | ------------- |
| 1    | MD5           |
| 2a   | Blowfish      |
| 5    | SHA-256       |
| 6    | SHA-512       |
| sha1 | SHA1crypt     |
| y    | Yescrypt      |
| gy   | Gost-yescrypt |
| 7    | Scrypt        |

Most modern Linux distributions (including Debian) use **yescrypt** by default. Older systems may still rely on weaker algorithms that are easier to crack.

---

## `/etc/security/opasswd`

The PAM module `pam_unix.so` can prevent password reuse. Previous passwords are stored in:

```text
/etc/security/opasswd
```

This file requires root privileges to read (unless permissions were altered).

Example:

```bash
$ sudo cat /etc/security/opasswd
```

```text
cry0l1t3:1000:2:$1$HjFAfYTG$qNDkF0zJ3v8ylCOrKB0kt0,$1$kcUjWZJX$E9uMSmiQeRh4pAAgzuvkq1
```

Key observation:

* `$1$` indicates **MD5**, which is significantly weaker than SHA-512
* Old password hashes can reveal patterns
* Users often reuse similar passwords across services

Identifying these patterns can greatly improve password recovery success.

---

## Cracking Linux Credentials

Once root access is obtained, password hashes can be extracted and cracked offline.

### Combining passwd and shadow

Use `unshadow` from **John the Ripper**:

```bash
sudo cp /etc/passwd /tmp/passwd.bak
sudo cp /etc/shadow /tmp/shadow.bak
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```

### Cracking with Hashcat

Example using SHA-512 (`-m 1800`):

```bash
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

### Note

This exact scenario is what **John the Ripper single crack mode** was specifically designed for, as it leverages user and system metadata to optimize cracking efficiency.
