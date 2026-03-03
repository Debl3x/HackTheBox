# Introduction to Server-side Attacks

Server-side attacks target the application or service provided by a server, whereas client-side attacks occur at the client's machine. Understanding and identifying the differences is essential for penetration testing and bug bounty hunting.

For instance, vulnerabilities like Cross-Site Scripting (XSS) target the web browser (client), while server-side attacks target the web server itself.

## Vulnerabilities Covered

- **Server-Side Request Forgery (SSRF)**
- **Server-Side Template Injection (SSTI)**
- **Server-Side Includes (SSI) Injection**
- **eXtensible Stylesheet Language Transformations (XSLT) Server-Side Injection**

## SSRF

Server-Side Request Forgery (SSRF) allows an attacker to manipulate a web application into sending unauthorized requests from the server. It often occurs when applications make HTTP requests to other servers based on user input. Successful exploitation can enable access to internal systems, bypass firewalls, and retrieve sensitive information.

## SSTI

Web applications use templating engines and server-side templates to generate responses dynamically, often based on user input. When an attacker injects template code, a Server-Side Template Injection vulnerability occurs. SSTI can lead to data leakage and full server compromise via remote code execution.

## SSI Injection

Similar to server-side templates, Server-Side Includes (SSI) generate HTML responses dynamically using directives embedded in HTML files. SSI can include content present across pages, such as headers or footers. When an attacker injects commands into SSI directives, SSI injection occurs, potentially leading to data leakage or remote code execution.

## XSLT Server-Side Injection

XSLT (Extensible Stylesheet Language Transformations) server-side injection occurs when an attacker manipulates XSLT transformations performed on the server. XSLT transforms XML documents into other formats like HTML. Attackers exploit weaknesses in how XSLT transformations are handled, allowing injection and execution of arbitrary code on the server.
