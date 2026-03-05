# Attacks on Authentication

We will categorize attacks on authentication based on the three types of authentication methods discussed in the previous section.

## Attacking Knowledge-based Authentication

Knowledge-based authentication is prevalent and comparatively easy to attack. This authentication method suffers from reliance on static personal information that can be potentially obtained, guessed, or brute-forced.

**Key vulnerabilities:**
- Social engineering
- Data breaches
- Guessing attacks

## Attacking Ownership-based Authentication

Ownership-based authentication is resistant to phishing and password-guessing attacks. Physical possession methods like hardware tokens or smart cards are inherently more secure.

**Advantages:**
- Difficult to acquire or replicate
- Resistant to phishing

**Disadvantages:**
- Cost and logistics of distribution
- Vulnerable to theft or cloning
- Susceptible to cryptographic attacks (e.g., NFC badge cloning)

## Attacking Inherence-based Authentication

Inherence-based authentication offers convenience—users simply provide biometric data like fingerprints or facial scans.

**Advantages:**
- No need to remember passwords
- Enhanced user experience

**Critical concern:**
- Irreversibly compromised if breached (users cannot change biometric features)
- Privacy and potential algorithmic bias issues
- 2019 example: Biometric smart lock breach exposed fingerprints and facial patterns of all users permanently
