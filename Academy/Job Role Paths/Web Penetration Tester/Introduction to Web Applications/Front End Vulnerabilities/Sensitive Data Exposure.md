# Sensitive Data Exposure

## Overview

All front-end components are executed client-side and don't directly threaten the back-end. However, they expose end-users to potential attacks. Front-end vulnerabilities targeting admin users can lead to unauthorized access, data breaches, and service disruption.

While penetration testing focuses mainly on back-end components, testing front-end vulnerabilities is critical—they can provide access to sensitive functionality (e.g., admin panels) and compromise the entire server.

## What is Sensitive Data Exposure?

Sensitive Data Exposure refers to sensitive data exposed in clear-text to end-users, typically found in the page source (HTML/JavaScript) of web applications.

**Viewing page source:**
- Right-click → "View Page Source"
- Keyboard shortcut: `Ctrl + U`
- Use web proxies like Burp Suite

Common exposed information includes:
- Login credentials
- Password hashes
- Hidden comments
- Exposed links/directories
- User information

## Example

**Login form source code:**
```html
<form action="action_page.php" method="post">
    <div class="container">
        <label for="uname"><b>Username</b></label>
        <input type="text" required>
        
        <label for="psw"><b>Password</b></label>
        <input type="password" required>
        
        <!-- TODO: remove test credentials test:test -->
        
        <button type="submit">Login</button>
    </div>
</form>
```

The HTML comment reveals test credentials (`test:test`), which may still be valid.

## Prevention Best Practices

- Remove unnecessary code and comments
- Classify data types and control client-side exposure
- Review code for hidden links and sensitive information
- Use JavaScript obfuscation or code packing
- Run automated tools to detect exposed data
  