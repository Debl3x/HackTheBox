# Intro to Command Injections

## Overview

A Command Injection vulnerability is among the most critical types of vulnerabilities. It allows execution of system commands directly on the back-end hosting server, potentially compromising the entire network. When a web application uses user-controlled input to execute system commands without proper validation, attackers can inject malicious payloads to execute arbitrary commands.

## What are Injections?

Injection vulnerabilities rank #3 in OWASP's Top 10 Web App Risks due to their high impact and prevalence. Injection occurs when user-controlled input is misinterpreted as part of a web query or executed code, subverting the intended outcome.

### Common Injection Types

| Type | Description |
|------|-------------|
| OS Command Injection | User input directly used as part of an OS command |
| Code Injection | User input within a function that evaluates code |
| SQL Injection | User input directly used in an SQL query |
| Cross-Site Scripting/HTML Injection | User input displayed on a web page without sanitization |

Other types include LDAP, NoSQL, HTTP Header, XPath, IMAP, and ORM injections. Any unsanitized user input in queries can allow attackers to escape boundaries and manipulate query intent.

## OS Command Injections

User-controlled input must directly or indirectly affect a web query that executes system commands. Most web languages provide functions to execute OS commands (e.g., for plugin installation or application execution).

### PHP Example

```php
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

This creates a vulnerable PDF in `/tmp/` using unsanitized user input, enabling arbitrary command execution.

### NodeJS Example

```javascript
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```

Both examples are vulnerable as they use unsanitized filename parameters. The vulnerability exists across all web frameworks and languages with command execution functions.
