# Authentication Bypass via Direct Access

## Overview
After discussing various attacks on flawed authentication implementations, this section highlights vulnerabilities that enable complete bypass of authentication mechanisms.

## Direct Access Vulnerability

### Concept
The most straightforward way to bypass authentication is to request protected resources directly from an unauthenticated context. If the web application doesn't properly verify authentication, attackers can access protected information.

### Example Scenario
Consider a web application that redirects users to `/admin.php` after authentication. If the app relies solely on the login page, attackers can access `/admin.php` directly.

### Vulnerable PHP Pattern
```php
if(!$_SESSION['active']) {
    header("Location: index.php");
}
```

**Problem**: The script continues execution after the redirect, sending protected information in the response body even though a 302 redirect is issued.

### Exploitation Technique

**Using Burp Suite:**
1. Enable Intercept
2. Browse to `/admin.php`
3. Right-click the request → Select **Do intercept > Response to this request**
4. Forward the request
5. Change the HTTP status code from `302 Found` to `200 OK`
6. Forward the response

The browser now displays the protected admin content instead of following the redirect.

### Mitigation
Use `exit` after the redirect to prevent further execution:

```php
if(!$_SESSION['active']) {
    header("Location: index.php");
    exit;
}
```

This ensures the script terminates and no protected information is sent in the response body.
