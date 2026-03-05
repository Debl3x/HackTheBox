# Authentication Bypass via Parameter Modification

Authentication implementations can be flawed when they depend on HTTP parameters, creating vulnerabilities that lead to authentication and authorization bypasses, including privilege escalation.

This vulnerability relates to authorization issues like Insecure Direct Object Reference (IDOR), covered in the Web Attacks module.

## Parameter Modification

### Scenario

After logging in with credentials `htb-stdnt`, you're redirected to `/admin.php?user_id=183`. However, the dashboard shows insufficient privileges:

```
Error: "Could not load admin data. Please check your privileges."
```

### Investigation

Removing the `user_id` parameter redirects you to the login screen, despite a valid session cookie. This indicates the parameter controls authentication.

### Bypass

Direct access to `/admin.php?user_id=183` bypasses authentication entirely. By guessing or brute-forcing an administrator's user ID, you can gain admin privileges by specifying it in the `user_id` parameter.

See: Brute-Force Attacks section for ID enumeration techniques.

## Related Vulnerabilities

More advanced bypasses include:

- **Type Juggling** → Whitebox Attacks module
- **Injection Vulnerabilities** → Injection Attacks, SQL Injection Fundamentals modules
- **Logic Bugs** → Parameter Logic Bugs module
