# Credential Hunting in Linux

Credential hunting is one of the first steps after gaining access to a Linux system. These *low-hanging fruits* can lead to privilege escalation within seconds or minutes and are a key part of the **local privilege escalation** process.

The goal is to identify passwords, keys, or credentials that can be reused to access other users or services on the system.

> Results vary greatly depending on the environment. Understanding the system’s role (web server, database server, user workstation, etc.) is essential to adapt the approach efficiently.

---

## Credential Sources

Credentials can be obtained from several categories:

1. **Files**

   * Configuration files
   * Databases
   * Notes
   * Scripts
   * Cron jobs
   * SSH keys

2. **History**

   * Command-line history
   * Log files

3. **Memory and Cache**

   * In-memory credentials
   * Active sessions

4. **Keyrings**

   * Browser-stored credentials
   * OS password managers

---

## 1. File Enumeration

Linux follows the principle that *everything is a file*. As a result, file enumeration is a fundamental step.

### 1.1 Configuration Files

Common extensions:

* `.conf`
* `.config`
* `.cnf`

> Note: Configuration files can be renamed or use non-standard filenames.

#### Searching for configuration files

```bash
for l in .conf .config .cnf; do
  echo -e "\nExtension: $l"
  find / -name "*$l" 2>/dev/null | grep -v "lib\|fonts\|share\|core"
done
```

#### Searching for credentials in `.cnf` files

```bash
for i in $(find / -name "*.cnf" 2>/dev/null | grep -v "doc\|lib"); do
  echo -e "\nFile: $i"
  grep "user\|password\|pass" "$i" 2>/dev/null | grep -v "#"
done
```

---

### 1.2 Databases

Searching for database files stored locally:

```bash
for l in .sql .db .*db .db*; do
  echo -e "\nDB extension: $l"
  find / -name "*$l" 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man"
done
```

Some databases (e.g., browser profiles, caches) may contain sensitive data.

---

### 1.3 Notes and Text Files

Notes may include credentials and are not always clearly named.

```bash
find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

---

### 1.4 Scripts

Automation scripts often contain hardcoded credentials.

Common extensions:

* `.sh`, `.py`, `.pl`, `.go`, `.jar`, `.c`

```bash
for l in .py .pyc .pl .go .jar .c .sh; do
  echo -e "\nExtension: $l"
  find / -name "*$l" 2>/dev/null | grep -v "doc\|lib\|headers\|share"
done
```

---

### 1.5 Cron Jobs

Cron jobs may execute scripts that embed credentials.

```bash
cat /etc/crontab
ls -la /etc/cron.*
```

Important locations:

* `/etc/crontab`
* `/etc/cron.d/`
* `/etc/cron.daily/`
* `/etc/cron.hourly/`
* `/etc/cron.weekly/`
* `/etc/cron.monthly/`

---

## 2. History and Logs

### 2.1 Command History

Shell history files may contain passwords passed as arguments.

```bash
tail -n 5 /home/*/.bash*
```

Relevant files:

* `.bash_history`
* `.bashrc`
* `.bash_profile`

---

### 2.2 Log Files

Logs can reveal authentication attempts, sudo usage, and system activity.

| File                  | Description                  |
| --------------------- | ---------------------------- |
| `/var/log/auth.log`   | Authentication logs (Debian) |
| `/var/log/secure`     | Authentication logs (RedHat) |
| `/var/log/syslog`     | System activity              |
| `/var/log/cron`       | Cron job activity            |
| `/var/log/httpd`      | Apache logs                  |
| `/var/log/mysqld.log` | MySQL logs                   |

#### Searching for interesting patterns

```bash
for i in /var/log/*; do
  grep "accepted\|failed\|ssh\|sudo\|COMMAND=" "$i" 2>/dev/null && echo "==> $i"
done
```

---

## 3. Memory and Cache

### 3.1 Mimipenguin

Tool used to extract credentials from memory (requires root privileges).

```bash
sudo python3 mimipenguin.py
```

---

### 3.2 LaZagne

A powerful credential extraction tool supporting many sources.

Supported sources include:

* Browsers
* WiFi
* SSH
* Git
* Environment variables
* Shadow
* Docker
* Keyrings

```bash
sudo python2.7 laZagne.py all
```

---

## 4. Browsers and Keyrings

### 4.1 Firefox

Firefox stores credentials in:

* `logins.json`
* `key4.db`

```bash
ls ~/.mozilla/firefox/ | grep default
cat ~/.mozilla/firefox/<profile>/logins.json | jq .
```

---

### 4.2 Decrypting Firefox Passwords

Recommended tool: **firefox_decrypt**

```bash
python3.9 firefox_decrypt.py
```

Select the profile to retrieve decrypted credentials.

---

### 4.3 Using LaZagne

```bash
python3 laZagne.py browsers
```

---

## Conclusion

Credential hunting on Linux relies on:

* **Methodical enumeration**
* **Understanding the system’s role**
* **Exploiting common misconfigurations and poor practices**

This phase is often decisive for achieving **fast and effective privilege escalation**.
