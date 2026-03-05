# What is Authentication

## Definition

Authentication is defined as "The process of verifying a claim that a system entity or system resource has a certain attribute value" in RFC 4949. In information security, authentication is the process of confirming an entity's identity, ensuring they are who they claim to be.

**Authorization** is an "approval that is granted to a system entity to access a system resource". While this module will not cover authorization in depth, understanding the major difference between it and authentication is vital.

| Aspect | Authentication | Authorization |
|--------|---|---|
| Purpose | Verifies identity | Determines access |
| Requires | Credentials | Policies |
| Timing | Occurs first | Follows authentication |

## Common Login Methods

The most widespread authentication method in web applications is login forms, where users enter their username and password. Examples include email providers, online banking, and HTB Academy:
https://academy.hackthebox.com/login

Authentication is the first line of defense against unauthorized access. As web application penetration testers, we aim to verify if authentication is implemented securely.

## Authentication Methods

Information technology systems can implement three major authentication categories:

### Knowledge-based
Something the user **knows**: passwords, passphrases, PINs, security questions.

### Ownership-based
Something the user **possesses**: ID cards, security tokens, authenticator apps.

### Inherence-based
Something the user **is or does**: fingerprints, facial patterns, voice recognition, signatures.

| Knowledge | Ownership | Inherence |
|-----------|-----------|-----------|
| Password | ID card | Fingerprint |
| PIN | Security Token | Facial Pattern |
| Security Question | Authenticator App | Voice Recognition |

## Single vs Multi-Factor Authentication

**Single-Factor Authentication (SFA)**: Relies on one method (e.g., password only).

**Multi-Factor Authentication (MFA)**: Combines multiple methods (e.g., password + TOTP). When exactly two factors are required, it's called **Two-Factor Authentication (2FA)**.
