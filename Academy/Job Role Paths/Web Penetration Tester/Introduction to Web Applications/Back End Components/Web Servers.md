# Web Servers

A web server is an application that runs on the back end server, handling all HTTP traffic from client-side browsers, routing requests to the appropriate pages, and responding back to clients. Web servers typically run on TCP ports 80 or 443.

## Workflow

Web servers accept HTTP requests and respond with appropriate HTTP response codes:

| Code | Description |
|------|-------------|
| **200** | OK - Request succeeded |
| **301** | Moved Permanently |
| **302** | Found - Temporary redirect |
| **400** | Bad Request - Invalid syntax |
| **401** | Unauthorized - Unauthenticated access |
| **403** | Forbidden - No access rights |
| **404** | Not Found |
| **405** | Method Not Allowed |
| **408** | Request Timeout |
| **500** | Internal Server Error |
| **502** | Bad Gateway |
| **504** | Gateway Timeout |

Web servers accept various input types (text, JSON, binary data) and route requests to destination pages, running necessary processes and returning responses.

### Example with cURL

```bash
curl -I https://academy.hackthebox.com
```

## Popular Web Servers

### Apache
- Most common web server (~40% of internet websites)
- Open-source, well-documented
- Supports PHP, .NET, Python, Perl
- Used by Apple, Adobe, Baidu

### NGINX
- Second most common (~30% of websites)
- Async architecture for high concurrency
- Popular among high-traffic websites (60% of top 100k sites)
- Used by Google, Facebook, Twitter, Netflix

### IIS
- Third most common (~15% of websites)
- Microsoft-developed for Windows servers
- Optimized for .NET and Active Directory integration
- Used by Microsoft, Office365, Stack Overflow

### Other Options
- Apache Tomcat (Java applications)
- Node.JS (JavaScript applications)
