# Command Injection Prevention

We should now have a solid understanding of how command injection vulnerabilities occur and how certain mitigations like character and command filters may be bypassed. This section discusses methods to prevent command injection vulnerabilities in web applications and properly configure webservers to prevent them.

## System Commands

We should always avoid using functions that execute system commands, especially with user input. Even indirect user influence can lead to command injection vulnerabilities.

**Best practice:** Use built-in functions instead. For example, in PHP, use `fsockopen()` instead of executing system commands to test if a host is alive.

If system command execution is necessary:
- Never directly use user input with these functions
- Always validate and sanitize user input on the back-end
- Limit usage and prefer built-in alternatives

## Input Validation

Validate and sanitize user input on both front-end and back-end. Input validation ensures data matches the expected format.

**PHP example:**
```php
if (filter_var($_GET['ip'], FILTER_VALIDATE_IP)) {
    // call function
} else {
    // deny request
}
```

**JavaScript example (regex for IP validation):**
```javascript
if(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ip)){
    // call function
} else {
    // deny request
}
```

You can also use libraries like `is-ip` for NodeJS or check language manuals (.NET, Java) for validation approaches.

## Input Sanitization

The most critical prevention step is **input sanitization**—removing non-necessary special characters after validation.

**PHP example:**
```php
$ip = preg_replace('/[^A-Za-z0-9.]/', '', $_GET['ip']);
```

**JavaScript example:**
```javascript
var ip = ip.replace(/[^A-Za-z0-9.]/g, '');
```

**NodeJS with DOMPurify:**
```javascript
import DOMPurify from 'dompurify';
var ip = DOMPurify.sanitize(ip);
```

For cases allowing special characters, use `escapeshellcmd()` in PHP or `escape()` in NodeJS, though escaping is less secure than removal.

## Server Configuration

Implement these security configurations:

- Enable Web Application Firewall (WAF): Apache `mod_security`, Cloudflare, Fortinet, Imperva
- Apply Principle of Least Privilege (PoLP): Run web server as low-privileged user (e.g., `www-data`)
- Disable dangerous functions: PHP `disable_functions=system,...`
- Limit scope: PHP `open_basedir = '/var/www/html'`
- Reject double-encoded and non-ASCII characters in URLs
- Avoid outdated libraries and modules (e.g., PHP CGI)

## Final Note

Combine secure coding practices with thorough penetration testing. Even in large codebases, a single vulnerable line can introduce risks.
