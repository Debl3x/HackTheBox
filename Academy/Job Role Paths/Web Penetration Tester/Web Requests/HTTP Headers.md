# HTTP Headers

## Overview
HTTP headers are key–value pairs used to pass metadata between the client and the server. They provide contextual information about the request or response, rather than the actual content itself. Some headers are exclusive to requests or responses, while others are shared.

Headers are grouped into several categories:
- General Headers
- Entity Headers
- Request Headers
- Response Headers
- Security Headers

---

## General Headers
General headers are used in both HTTP requests and responses. They describe the message itself rather than the content being transferred.

| Header | Example | Description |
|------|--------|-------------|
| Date | `Date: Wed, 16 Feb 2022 10:38:44 GMT` | Indicates the date and time the message was generated, typically in UTC. |
| Connection | `Connection: close` | Controls whether the network connection remains open after the request. Common values are `close` and `keep-alive`. |

---

## Entity Headers
Entity headers describe the **content (entity)** being transferred. They commonly appear in responses and in requests that include a body (e.g. POST, PUT).

| Header | Example | Description |
|------|--------|-------------|
| Content-Type | `Content-Type: text/html` | Specifies the media type and optional character encoding of the content. |
| Media-Type | `Media-Type: application/pdf` | Similar to Content-Type; helps the server interpret the transferred data. |
| Boundary | `boundary="b4e4fbd93540"` | Separates multiple parts of a message, commonly used in form data. |
| Content-Length | `Content-Length: 385` | Indicates the size of the message body in bytes. |
| Content-Encoding | `Content-Encoding: gzip` | Specifies transformations applied to the data, such as compression. |

---

## Request Headers
Request headers are sent by the client and describe request-specific attributes, not the content itself.

| Header | Example | Description |
|------|--------|-------------|
| Host | `Host: www.inlanefreight.com` | Identifies the target host. Essential for virtual hosting and enumeration. |
| User-Agent | `User-Agent: curl/7.77.0` | Identifies the client software, version, and operating system. |
| Referer | `Referer: http://www.inlanefreight.com/` | Indicates the source of the request. Can be spoofed and should not be trusted blindly. |
| Accept | `Accept: */*` | Defines which media types the client can process. |
| Cookie | `Cookie: PHPSESSID=b4e4fbd93540` | Sends stored cookie data to maintain sessions and state. |
| Authorization | `Authorization: BASIC cGFzc3dvcmQK` | Provides authentication credentials or tokens for client identification. |

---

## Response Headers
Response headers are sent by the server and provide additional context about the response.

| Header | Example | Description |
|------|--------|-------------|
| Server | `Server: Apache/2.2.14 (Win32)` | Identifies the web server software and version. |
| Set-Cookie | `Set-Cookie: PHPSESSID=b4e4fbd93540` | Sends cookies to be stored by the client for future requests. |
| WWW-Authenticate | `WWW-Authenticate: BASIC realm="localhost"` | Indicates the authentication method required to access the resource. |

---

## Security Headers
Security headers enforce browser-side security policies and reduce the attack surface of web applications.

| Header | Example | Description |
|------|--------|-------------|
| Content-Security-Policy | `script-src 'self'` | Restricts which resources the browser is allowed to load, mitigating XSS attacks. |
| Strict-Transport-Security | `max-age=31536000` | Forces all communication to use HTTPS, preventing protocol downgrade attacks. |
| Referrer-Policy | `origin` | Controls how much referrer information is shared with external sites. |

---

## Working with Headers Using cURL
cURL provides multiple options for inspecting and modifying HTTP headers.

### Viewing Headers
- Response headers only (HEAD request):
```bash
curl -I https://example.com
````

* Headers and response body:

```bash
curl -i https://example.com
```

### Modifying Request Headers

Custom headers can be set using the `-H` flag. Some headers have dedicated options:

* Set User-Agent:

```bash
curl https://example.com -A 'Mozilla/5.0'
```

---

## Browser Developer Tools

Browser DevTools provide an interactive way to inspect headers.

* Open DevTools using `F12` or `CTRL + SHIFT + I`
* Navigate to the **Network** tab
* Select a request to view:

  * Request headers
  * Response headers
  * Raw header format
  * Associated cookies

---

## Key Takeaways

* HTTP headers carry critical metadata for requests and responses
* Different header categories serve different roles
* Security headers help protect users from common web attacks
* cURL and browser DevTools are essential for header inspection and manipulation.
