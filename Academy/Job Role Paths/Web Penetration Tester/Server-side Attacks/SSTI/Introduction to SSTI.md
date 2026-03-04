# Introduction to SSTI

## Overview

Server-side Template Injection (SSTI) occurs when an attacker injects templating code into a template that is later rendered by the server. If malicious code is injected, the server may execute it during rendering, allowing complete control of the server.

## How SSTI Works

### Secure Template Rendering

Template engines handle user input securely when provided as values to the rendering function. In this case:
- Values are inserted into corresponding template locations
- No code within the values is executed

### SSTI Vulnerability Scenarios

SSTI occurs when an attacker controls the template parameter, as template engines execute code within templates. Common scenarios include:

1. **Direct template injection** - User input is inserted into the template before rendering
2. **Multi-pass rendering** - User input from one rendering becomes part of the template string in a second rendering
3. **User-modifiable templates** - Web applications allowing users to modify or submit templates

### Best Practices

To prevent SSTI, ensure user input is always provided as rendering function values, never within the template string itself.
