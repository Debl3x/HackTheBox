# Intro to HTTP Verb Tampering

## Overview

The HTTP protocol accepts various HTTP methods (verbs) at the beginning of requests. Web servers and applications may be configured to accept specific methods and perform actions based on request type.

While GET and POST are most common, any HTTP method can be sent. If a server isn't restricted to required methods and the application doesn't handle unexpected ones, attackers may exploit this to access restricted functionality or bypass security controls.

## HTTP Methods

HTTP supports 9 different verbs. Common ones include:

| Verb | Description |
|------|-------------|
| HEAD | Like GET, but returns headers only (no body) |
| PUT | Writes request payload to specified location |
| DELETE | Deletes resource at specified location |
| OPTIONS | Lists accepted HTTP verbs by the server |
| PATCH | Applies partial modifications to resources |

Sensitive methods like PUT and DELETE can write or delete files. Misconfigured servers or insecure applications enable HTTP Verb Tampering attacks.

## Vulnerability Types

### Insecure Web Server Configuration

Authentication may be limited to specific HTTP methods, leaving others accessible without authentication.

**Example:**
```xml
<Limit GET POST>
    Require valid-user
</Limit>
```

An attacker could use HEAD to bypass authentication entirely.

### Insecure Coding Practices

Developers may apply security filters to some HTTP methods but not others. For example, a sanitization filter on GET parameters won't protect POST data.

**Example:**
```php
$pattern = "/^[A-Za-z\s]+$/";
if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
}
```

Here, GET is validated but `$_REQUEST["code"]` includes POST data, enabling SQL Injection via POST requests.

**Note:** Insecure coding is more common than server misconfigurations.
