# Common Web Vulnerabilities

During penetration testing, we may manually identify vulnerabilities in internally developed applications or those without public exploits. Misconfigurations in publicly available applications can also introduce security risks. The vulnerabilities below are among the most common in web applications and are part of the OWASP Top 10.

### Broken Authentication/Access Control

**Broken Authentication** allows attackers to bypass authentication mechanisms, such as logging in without valid credentials or escalating privileges to administrator level.

**Broken Access Control** enables unauthorized access to restricted pages and features, such as a normal user accessing the admin panel.

**Example:** College Management System 1.2 contains an authentication bypass vulnerability. Using `' or 0=0 #` in the email field and any password bypasses login validation.

### Malicious File Upload

Unvalidated file uploads allow attackers to execute malicious scripts on the remote server. Despite being a basic vulnerability, many developers fail to implement proper file validation, and existing checks can often be bypassed.

**Example:** WordPress Plugin Responsive Thumbnail Slider 1.0 allows arbitrary file uploads via double extensions (e.g., `shell.php.jpg`). A Metasploit module exploits this vulnerability automatically.

### Command Injection

When web applications execute OS commands based on user input, attackers can inject additional commands for execution. Weak input sanitization allows bypassing filters and gaining direct control over the back-end server.

**Example:** WordPress Plugin Plainview Activity Monitor 20161228 allows command injection by appending `| COMMAND...` to the IP parameter.

### SQL Injection (SQLi)

SQL Injection occurs when user-supplied input is directly included in SQL queries without proper filtering or validation.

**Example:**
```php
$query = "select * from users where name like '%$searchInput%'";
```

Attackers can inject malicious SQL to authenticate without credentials, extract database data, or compromise the hosting server.

**Example:** College Management System 1.2 suffers from SQL injection, allowing authentication bypass and data extraction.

### Conclusion

These vulnerabilities are fundamental to information security. Gaining a solid understanding of each will strengthen your security assessment skills. Later modules will cover each vulnerability in depth.
