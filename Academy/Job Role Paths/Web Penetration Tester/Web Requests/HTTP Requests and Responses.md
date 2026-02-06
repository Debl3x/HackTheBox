# HTTP Requests and Responses

## Overview
HTTP communication is based on an exchange between a **client** and a **server**, consisting of:
- An **HTTP request** sent by the client (e.g. browser or cURL)
- An **HTTP response** returned by the server

The request defines what the client wants, while the response indicates whether the request was successful and may include the requested data.

---

## HTTP Requests
An HTTP request contains all information needed by the server to process an action, including the target resource, parameters, headers, and optional data.

### Request Structure
The first line of an HTTP request contains three space-separated fields:

| Field | Example | Description |
|-----|--------|-------------|
| Method | `GET` | Specifies the action to perform |
| Path | `/users/login.html` | Resource path, optionally including a query string |
| Version | `HTTP/1.1` | HTTP protocol version |

After the request line, the request includes **headers** such as:
- `Host`
- `User-Agent`
- `Cookie`

Headers define attributes and metadata about the request. They are followed by a blank line, after which an optional **request body** may appear.

### HTTP Versions
- **HTTP/1.x**: Transmits requests in clear text, using new lines to separate fields
- **HTTP/2.x**: Uses a binary format with structured data representation

---

## HTTP Responses
After processing a request, the server sends an HTTP response.

### Response Structure
The first line of a response contains:
- The **HTTP version**
- The **status code and message** (e.g. `200 OK`)

Following this are **response headers**, similar in structure to request headers. Common headers include:
- `Date`
- `Server`
- `Set-Cookie`
- `Content-Type`
- `Content-Length`

After a blank line, the response may include a **response body**.

### Response Body
The response body usually contains HTML, but it can also include:
- JSON or XML data
- Images, scripts, or stylesheets
- Documents such as PDFs

---

## Inspecting Requests with cURL
By default, cURL only displays the response body. To inspect the full HTTP exchange, the verbose flag can be used.

### Verbose Output
```bash
curl inlanefreight.com -v
````

This displays:

* The full HTTP request sent by the client
* The full HTTP response returned by the server

The output includes request headers, response headers, status codes, and the response body.

Using the `-vvv` flag produces even more detailed debugging output.

---

## Browser Developer Tools

Modern browsers include **Developer Tools (DevTools)** that are useful for inspecting HTTP traffic.

### Accessing DevTools

* Open with `CTRL + SHIFT + I` or `F12`
* Available in browsers such as Chrome and Firefox

### Network Tab

The **Network** tab displays all HTTP requests made by a page, including:

* Request method (e.g. GET)
* Requested URL and path
* Response status code

Filters can be applied to quickly locate specific requests, which is especially useful for pages that generate many network calls.

---

## Key Takeaways

* HTTP communication relies on request–response exchanges
* Requests define the action and target resource
* Responses indicate success or failure and may include data
* cURL and browser DevTools are essential tools for analyzing HTTP traffic.
