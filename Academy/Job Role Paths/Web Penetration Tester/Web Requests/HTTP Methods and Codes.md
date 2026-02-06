# HTTP Methods and Codes

## Overview
HTTP defines a set of **methods** that specify how a client wants the server to process a request, and **status codes** that indicate the outcome of that request. Together, they form the core control mechanism of HTTP communication.

The HTTP method is included in the first line of the request, while the status code is returned in the response headers.

---

## HTTP Request Methods
HTTP methods instruct the server on the action to perform on a given resource.

| Method | Description |
|------|-------------|
| GET | Requests a specific resource. Optional data can be passed using query strings in the URL (e.g. `?param=value`). |
| POST | Sends data to the server in the request body. Commonly used for form submissions, logins, and file uploads. |
| HEAD | Requests only the response headers of a GET request, without the response body. Useful for checking metadata such as content length. |
| PUT | Creates or replaces a resource on the server. If not properly secured, it may allow malicious uploads. |
| DELETE | Removes a resource from the server. Misconfiguration can lead to denial-of-service by deleting critical files. |
| OPTIONS | Returns information about the server’s supported HTTP methods. |
| PATCH | Applies partial modifications to an existing resource. |

Most traditional web applications primarily use **GET** and **POST**, while RESTful APIs frequently rely on **PUT**, **DELETE**, and **PATCH** for data manipulation.

---

## HTTP Status Codes
Status codes are used by the server to inform the client of the result of a request. They are grouped into five classes.

### Status Code Classes
| Class | Description |
|------|-------------|
| 1xx | Informational responses that do not affect request processing |
| 2xx | Indicates successful request processing |
| 3xx | Indicates client redirection |
| 4xx | Client-side errors due to invalid or unauthorized requests |
| 5xx | Server-side errors during request processing |

### Common Status Codes
| Code | Description |
|------|-------------|
| 200 OK | The request succeeded and the response contains the requested resource. |
| 302 Found | Redirects the client to a different URL, often used after successful authentication. |
| 400 Bad Request | The request is malformed or improperly structured. |
| 403 Forbidden | The client is not authorized to access the resource, or the request was blocked. |
| 404 Not Found | The requested resource does not exist on the server. |
| 500 Internal Server Error | The server encountered an unexpected condition and could not process the request. |

Beyond standard HTTP status codes, some providers and platforms implement custom codes for additional context.

---

## Key Takeaways
- HTTP methods define how the server should handle a request.
- Status codes communicate the outcome of request processing.
- GET and POST dominate traditional web traffic, while APIs rely on additional methods.
- Proper handling and restriction of methods is critical for web security.
