# Identifying SSTI

## Overview

Before exploiting an SSTI vulnerability, you must confirm its presence and identify the template engine. Each template engine uses different syntax and functions for exploitation.

## Confirming SSTI

Identify SSTI similarly to other injection vulnerabilities (e.g., SQL injection) by injecting special characters with semantic meaning and observing the application's behavior.

**Test string:**
```
${{<%[%'"}}%\.
```

This string contains special characters meaningful to popular template engines. A vulnerable application should throw an error due to syntax violation.

**Example:**
- **Request:** `http://<SERVER_IP>:<PORT>/` (enter test string in form)
- **Response:** `Internal Server Error: The server encountered an error and cannot complete your request.`

While an error doesn't confirm SSTI, it increases suspicion of vulnerability.

## Identifying the Template Engine

Determine the template engine by exploiting behavioral differences between engines. Use a decision tree flowchart to test payloads:

1. **First payload:** `${7*7}`
    - If executed → follow green path
    - If not executed → follow red path

2. **Second payload:** `{{7*7}}`
    - Tests if Jinja2, Twig, or similar engines

3. **Third payload:** `{{7*'7'}}`
    - **Jinja result:** `7777777` (string repetition)
    - **Twig result:** `49` (arithmetic operation)

**Example flow:**
- `${7*7}` returns literal string → not executed (red)
- `{{7*7}}` returns `49` → executed (green)
- `{{7*'7'}}` returns `49` → **Twig identified**
