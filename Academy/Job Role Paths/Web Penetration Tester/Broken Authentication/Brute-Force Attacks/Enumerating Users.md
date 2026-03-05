# Enumerating Users

User enumeration vulnerabilities occur when a web application responds differently to registered and valid versus invalid inputs for authentication endpoints. These vulnerabilities often appear in login, registration, and password reset functions.

Web developers frequently overlook user enumeration, assuming usernames aren't sensitive data. However, usernames can be considered confidential when they're the primary authentication identifier. Since users reuse usernames across services (web apps, FTP, RDP, SSH), identifying valid usernames enables further attacks. Most web applications use usernames or email addresses as primary identifiers, making enumeration feasible.

## User Enumeration Theory

Protecting against username enumeration can negatively impact user experience. Applications that reveal username existence help legitimate users correct typos, but also assist attackers. Even mature applications like WordPress allow enumeration by default.

**Invalid username example:**
> "Unknown username. Check again or try your email address."

**Valid username example:**
> "The password you entered for the username editor is incorrect."

User enumeration can be a deliberate security trade-off. For example, chat applications need user search functionality to operate effectively, even though it enables enumeration. It's not always a vulnerability but should be avoided when possible. Alternative approaches: use email instead of username for login.

## Enumerating Users via Differing Error Messages

Attackers use wordlists of usernames for enumeration. Common sources include:
- SecLists (wordlist collections)
- Web application crawling
- OSINT on social media and company profiles

**Example - invalid username:**
> "Unknown user."

**Example - valid username with wrong password:**
> "Invalid credentials."

**Using ffuf to enumerate users:**

```bash
ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt \
    -u http://172.17.0.2/index.php \
    -X POST \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "username=FUZZ&password=invalid" \
    -fr "Unknown user"
```

This identifies valid usernames by filtering responses containing "Unknown user". Once valid usernames are found, password brute-forcing can follow.

## User Enumeration via Side-Channel Attacks

Beyond direct response differences, attackers can exploit side channels—indirect information leakage. Response timing is a common side channel: if applications perform database lookups only for valid usernames, response time differences may reveal valid accounts, even with identical responses. See the Whitebox Attacks module for timing-based enumeration techniques.
