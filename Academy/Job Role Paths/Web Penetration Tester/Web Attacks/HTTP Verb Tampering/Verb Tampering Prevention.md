# Verb Tampering Prevention

After seeing a few ways to exploit Verb Tampering vulnerabilities, let's see how we can protect ourselves against these types of attacks by preventing Verb Tampering. Insecure configurations and insecure coding are what usually introduce Verb Tampering vulnerabilities. In this section, we will look at samples of vulnerable code and configurations and discuss how we can patch them.

## Insecure Configuration

HTTP Verb Tampering vulnerabilities can occur in most modern web servers, including Apache, Tomcat, and ASP.NET. The vulnerability usually happens when we limit a page's authorization to a particular set of HTTP verbs/methods, which leaves the other remaining methods unprotected.

### Apache Configuration

The following is a vulnerable Apache configuration (in `000-default.conf` or `.htaccess`):

```xml
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit>
</Directory>
```

The `<Limit GET>` keyword only protects GET requests, leaving the page accessible through POST, HEAD, OPTIONS, and other methods.

### Tomcat Configuration

The same vulnerability in Tomcat's `web.xml`:

```xml
<security-constraint>
    <web-resource-collection>
        <url-pattern>/admin/*</url-pattern>
        <http-method>GET</http-method>
    </web-resource-collection>
    <auth-constraint>
        <role-name>admin</role-name>
    </auth-constraint>
</security-constraint>
```

### ASP.NET Configuration

The same vulnerability in ASP.NET's `web.config`:

```xml
<system.web>
    <authorization>
        <allow verbs="GET" roles="admin">
            <deny verbs="GET" users="*">
        </deny>
        </allow>
    </authorization>
</system.web>
```

### Prevention Best Practices

- Always allow/deny **all HTTP verbs and methods**, not just specific ones
- Use safe keywords: `LimitExcept` (Apache), `http-method-omission` (Tomcat), `add/remove` (ASP.NET)
- Disable/deny all HEAD requests unless specifically required

## Insecure Coding

Identifying and patching insecure code is more challenging than fixing configurations. Vulnerabilities often result from inconsistent use of HTTP parameters across functions.

### Vulnerable Example

```php
if (isset($_REQUEST['filename'])) {
    if (!preg_match('/[^A-Za-z0-9. _-]/', $_POST['filename'])) {
        system("touch " . $_REQUEST['filename']);
    } else {
        echo "Malicious Request Denied!";
    }
}
```

**Issue:** The filter checks `$_POST['filename']`, but the command uses `$_REQUEST['filename']` (which includes GET parameters). An attacker can bypass filtering by sending malicious input via GET.

### Prevention Best Practices

- **Be consistent** with HTTP methods across all functionality
- **Expand scope** of security filters to test all request parameters
- Use universal parameter functions:

| Language | Function |
|----------|----------|
| PHP | `$_REQUEST['param']` |
| Java | `request.getParameter('param')` |
| C# | `Request['param']` |

When security filters cover all methods, you effectively prevent HTTP Verb Tampering vulnerabilities.
