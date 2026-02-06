# Hypertext Transfer Protocol Secure (HTTPS)

## Overview
HTTPS (Hypertext Transfer Protocol Secure) was designed to address a major weakness of HTTP: **data transmission in clear text**. With HTTP, any intermediary between the client and the server can intercept traffic and perform a Man-in-the-Middle (MITM) attack to read or manipulate sensitive data such as credentials.

HTTPS mitigates this risk by **encrypting all communications** between the client and the server. As a result, even if traffic is intercepted, the data cannot be meaningfully extracted. For this reason, HTTPS has become the standard on the modern web, while HTTP is gradually being phased out by browsers.

---

## Why HTTPS Is Necessary
In an HTTP request, sensitive information such as usernames and passwords is transmitted in plain text. Anyone monitoring the network (e.g. on public Wi-Fi) can easily capture and reuse this information.

In contrast, HTTPS traffic is encrypted using TLS. Intercepted HTTPS traffic appears as encrypted binary data, preventing attackers from extracting credentials or other sensitive information.

Websites using HTTPS can be identified by:
- The `https://` scheme in the URL
- A lock icon in the browser address bar indicating a secure connection

---

## DNS and Privacy Considerations
While HTTPS encrypts the request and response data, **DNS queries may still be sent in clear text**, potentially revealing the visited domain. To mitigate this risk, it is recommended to:
- Use encrypted DNS resolvers
- Use a VPN to encrypt all network traffic

---

## HTTPS Communication Flow
1. The user attempts to access a website using `http://`.
2. The browser resolves the domain name via DNS.
3. The initial request is sent to port 80 (HTTP).
4. If HTTPS is enforced, the server responds with a `301 Moved Permanently` redirect to HTTPS.
5. The client reconnects to the server on port 443.
6. A TLS handshake is performed:
   - Client sends a *Client Hello*
   - Server replies with a *Server Hello* and its SSL/TLS certificate
   - Certificates are exchanged and verified
   - An encrypted session is established
7. Normal HTTP communication continues over the encrypted channel.

This description represents a high-level overview of the TLS process.

---

## Security Notes
- In certain scenarios, attackers may attempt **HTTP downgrade attacks**, forcing HTTPS connections to fall back to HTTP.
- Modern browsers, servers, and web applications implement protections to prevent such attacks.

---

## Using HTTPS with cURL
cURL automatically handles HTTPS, including TLS handshakes and encryption.

### Invalid SSL Certificates
If a website presents an invalid or outdated SSL certificate, cURL will refuse the connection by default to prevent MITM attacks:
```bash
curl https://example.com
````

This behavior is similar to modern web browsers, which warn users before allowing access.

### Skipping Certificate Validation

For testing environments or local applications without valid certificates, certificate validation can be bypassed using the `-k` flag:

```bash
curl -k https://example.com
```

This allows the request to proceed but should **never** be used in production environments.

---

## Key Takeaways

* HTTPS encrypts web traffic to protect confidentiality and integrity.
* It prevents credential leakage and reduces MITM attack risks.
* TLS is responsible for certificate exchange and secure session setup.
* cURL enforces certificate validation by default for security reasons.
