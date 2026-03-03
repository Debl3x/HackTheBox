# Introduction to SSRF

SSRF vulnerabilities are part of OWASP's Top 10. This type of vulnerability occurs when a web application fetches additional resources from a remote location based on user-supplied data, such as a URL.

## Server-side Request Forgery

When a web server fetches remote resources based on user input, an attacker might coerce it into making requests to arbitrary URLs. This makes the application vulnerable to SSRF. While initially seeming minor, SSRF can have devastating consequences depending on the web application's configuration.

Additionally, if the application relies on user-supplied URL schemes or protocols, attackers can manipulate them to cause undesired behavior. Common URL schemes exploited in SSRF attacks include:

- **http:// and https://** — Fetch content via HTTP/S requests; can bypass WAFs, access restricted endpoints, or reach internal network endpoints
- **file://** — Reads files from the local file system; enables Local File Inclusion (LFI) attacks on web servers
- **gopher://** — Sends arbitrary bytes to specified addresses; useful for crafting HTTP POST requests, SMTP communication, or database interactions

For advanced SSRF exploitation techniques such as filter bypasses and DNS rebinding, see the Modern Web Exploitation Techniques module.
