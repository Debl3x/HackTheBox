# Introduction

Web applications are interactive applications that run on web browsers, adopting a client-server architecture. They have front-end components (user interface) running on the client-side (browser) and back-end components (source code) running on the server-side.

This architecture allows organizations to host powerful applications with real-time control while remaining accessible worldwide. Common examples include Gmail, Amazon, and Google Docs.

Unlike giant providers, any developer can create and host web applications. Today, millions of web applications serve billions of users daily.

## Web Applications vs. Websites

**Websites** (Web 1.0) are static pages that don't change in real-time. Content updates require manual developer edits and lack interactive functionality.

**Web Applications** (Web 2.0) deliver dynamic, interactive content based on user interactions:
- Fully functional with multiple features
- Modular design
- Run on any display size
- Platform-independent

## Web Applications vs. Native OS Applications

Web applications are **platform-independent** and don't require installation, while native OS applications are platform-specific and consume local storage.

**Advantages of web applications:**
- Version unity (all users access the same version)
- Reduced maintenance costs (single-location updates)

**Advantages of native applications:**
- Faster performance
- Access to native OS libraries and hardware
- Greater capabilities

Hybrid and progressive web applications now bridge this gap using modern frameworks.

## Web Application Distribution

**Open-source** applications (customizable): WordPress, OpenCart, Joomla

**Closed-source** applications (proprietary): Wix, Shopify, DotNetNuke

## Security Risks of Web Applications

Web applications present significant attack surfaces due to:
- Worldwide accessibility
- Automated attack tools
- Complex and advanced designs
- Access to sensitive databases

A successful attack can compromise user data, cause business interruption, and expose corporate information. Organizations must test applications regularly, patch vulnerabilities promptly, and implement secure coding practices.

### Testing Approach

The **OWASP Web Security Testing Guide** is a standard methodology. Testing typically includes:
1. Front-end review (HTML, CSS, JavaScript) for XSS and data exposure
2. Core functionality testing (browser-webserver interaction)
3. Technology enumeration and exploitation
4. Authenticated and unauthenticated perspectives

## Attacking Web Applications

Most companies have web applications in their external perimeter, from simple websites to complex applications with user roles. Web applications provide vast attack surfaces due to their constantly changing nature.

### Common Web Application Vulnerabilities

| Vulnerability | Real-world Impact |
|---|---|
| **SQL Injection** | Access to sensitive data, file manipulation, RCE |
| **File Inclusion** | Reading source code, discovering hidden pages |
| **Unrestricted File Upload** | Uploading malicious code for server control |
| **IDOR** | Accessing other users' files or functionality |
| **Broken Access Control** | Privilege escalation during registration |

A single vulnerability can be exploited and chained with others to compromise databases, extract credentials, and pivot into corporate networks.

### Key Takeaway

Web application security is critical for modern organizations. A well-rounded penetration tester with strong web application knowledge can identify flaws others overlook, especially when vulnerabilities are chained together to maximize impact.
