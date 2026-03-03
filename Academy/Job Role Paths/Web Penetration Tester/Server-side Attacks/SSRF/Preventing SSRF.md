# Preventing SSRF

After discussing the identification and exploitation of SSRF vulnerabilities, we will now delve into SSRF prevention and mitigation techniques.

## Prevention

Mitigations and countermeasures against SSRF vulnerabilities can be implemented at the web application or network layers.

### Application Layer

If the web application fetches data from a remote host based on user input, implement:

- **Whitelist validation**: Check remote origin against an allowlist to prevent requests to arbitrary origins
- **Protocol restriction**: Hardcode or whitelist allowed URL schemes and protocols
- **Input sanitization**: Validate and sanitize all user input to prevent unexpected behavior

### Network Layer

- **Firewall rules**: Implement restrictive outgoing request policies to drop requests to unexpected systems
- **Network segmentation**: Isolate internal systems to prevent SSRF exploitation

For more details, see the [OWASP SSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html).
