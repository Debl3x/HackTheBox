# Bypassing Security Filters

## Overview

HTTP Verb Tampering vulnerabilities often arise from insecure coding practices that fail to validate all HTTP methods. Security filters may check only specific methods (e.g., POST) for malicious payloads, allowing attackers to bypass them using alternative methods like GET.

## Identify

In the File Manager web application, attempting to create a file with special characters (e.g., `test;`) returns:

```
Malicious Request Denied!
```

This indicates backend filters are blocking injection attempts. However, HTTP Verb Tampering may bypass these filters.

## Exploit

### Step 1: Change Request Method

Intercept the request in Burp Suite and change the method from POST to GET:

```
GET /api/files?filename=test%3B HTTP/1.1
Host: SERVER_IP:PORT
```

Result: The file is created without triggering the security filter.

### Step 2: Attempt Command Injection

Use a payload designed to execute multiple commands:

```
file1; touch file2;
```

Encode and send as GET parameter:

```
GET /api/files?filename=file1%3B+touch+file2%3B HTTP/1.1
```

### Step 3: Verify Exploitation

Both `file1` and `file2` are created, confirming:
- The security filter was bypassed
- Command injection was successfully achieved

## Conclusion

This vulnerability demonstrates how HTTP Verb Tampering can completely circumvent security filters, exposing underlying injection vulnerabilities that would otherwise be protected.
