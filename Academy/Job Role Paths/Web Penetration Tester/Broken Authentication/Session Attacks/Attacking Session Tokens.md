# Attacking Session Tokens

## Overview

Session tokens are unique identifiers that web applications use to identify users. They're tied to a user's session, so if an attacker obtains a valid session token, they can impersonate that user and hijack their session.

## Brute-Force Attack

### Weak Token Randomness

If a session token lacks sufficient randomness or cryptographic strength, it can be brute-forced. This occurs when tokens are too short or contain static data with insufficient entropy.

**Example: Four-character token**
```
Set-Cookie: session=a5fd
```

Such short tokens are easily brute-forced using standard brute-force techniques.

### Partially Static Tokens

More commonly, tokens have sufficient length but contain hardcoded prepended/appended values with only a small dynamic portion.

**Example: 32-character token with limited entropy**
```
2c0c58b27c71a2ec5bf2b4b6e892b9f9
2c0c58b27c71a2ec5bf2b4546092b9f9
2c0c58b27c71a2ec5bf2b497f592b9f9
2c0c58b27c71a2ec5bf2b48bcf92b9f9
2c0c58b27c71a2ec5bf2b4735e92b9f9
```

Analysis reveals the structure: `2c0c58b27c71a2ec5bf2b4` + 4 random chars + `92b9f9`. Only 4 characters vary, making enumeration trivial.

### Incrementing Session Identifiers

**Example: Sequential tokens**
```
141233
141234
141237
141238
141240
```

Simply incrementing or decrementing the token reveals other active sessions.

## Attacking Predictable Session Tokens

### Base64-Encoded Tokens

Session tokens may contain encoded data that's tamperable:

```
Set-Cookie: session=dXNlcjFodGItc3RkbnQ7cm9sZT11c2Vy
```

Decoding reveals:
```bash
$ echo -n dXNlcjFodGItc3RkbnQ7cm9sZT11c2Vy | base64 -d
user=htb-stdnt;role=user
```

**Forging an admin token:**
```bash
$ echo -n 'user=htb-stdnt;role=admin' | base64
dXNlcjFodGItc3RkbnQ7cm9sZT1hZG1pbg==
```

### Hexadecimal-Encoded Tokens

```
Set-Cookie: session=757365723d6874622d73746d6e743b726f6c653d75736572
```

**Forging with hex encoding:**
```bash
$ echo -n 'user=htb-stdnt;role=admin' | xxd -p
757365723d6874622d7374646e743b726f6c653d61646d696e
```

### Encryption-Based Tokens

Session tokens may encrypt data sequences. Weak cryptographic algorithms or improper handling can lead to privilege escalation. These are challenging to attack without source code access.

## Mitigation

Ensure session tokens provide:
- Sufficient cryptographic randomness
- No hardcoded static values
- No predictable patterns
- No tamperable encoded data
- Strong encryption (if used)

Analyze multiple captured tokens to verify randomness before deployment.
