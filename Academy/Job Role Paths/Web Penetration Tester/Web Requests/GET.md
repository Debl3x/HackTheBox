# GET

## Overview
When a user visits a URL, the browser issues a **GET request** by default to retrieve the requested resource. After loading the initial page, the browser may send additional requests using other HTTP methods to fetch related resources or data. These interactions can be observed using the browser’s Network tab.

Monitoring GET requests is essential for understanding how a web application communicates with its backend and is a common technique in web assessments and bug bounty investigations.

---

## HTTP Basic Authentication
Some resources are protected using **HTTP Basic Authentication**, which is enforced directly by the web server rather than the web application itself. Unlike form-based authentication, credentials are sent via HTTP headers.

When accessing a protected resource without credentials, the server responds with:
- Status code `401 Unauthorized`
- A `WWW-Authenticate` header indicating Basic authentication

### Authenticating with cURL
Credentials can be provided using the `-u` flag:
```bash
curl -u admin:admin http://<SERVER_IP>:<PORT>/
````

Another supported method is embedding credentials directly in the URL:

```bash
curl http://admin:admin@<SERVER_IP>:<PORT>/
```

Both methods result in an authenticated request if the credentials are valid.

---

## Authorization Header

With HTTP Basic Authentication, credentials are sent using the `Authorization` header:

```
Authorization: Basic <base64(username:password)>
```

For example, `admin:admin` is encoded as `YWRtaW46YWRtaW4=`.

The header can be manually set using cURL:

```bash
curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/
```

Modern authentication mechanisms often use the `Bearer` scheme with tokens (e.g. JWT) instead of Basic authentication.

---

## GET Parameters

GET requests can include parameters directly in the URL using a query string:

```
/search.php?search=le
```

These parameters are commonly used to retrieve filtered or dynamic data.

By inspecting the Network tab in browser DevTools, it is possible to:

* Identify backend endpoints used by the application
* Observe query parameters and request paths
* Understand how user input is processed

---

## Reproducing GET Requests

Browser DevTools provide convenient options to replicate requests:

* **Copy as cURL**: Generates a cURL command with the same request details
* **Copy as Fetch**: Generates a JavaScript Fetch request

These copied commands can be executed directly in a terminal or browser console to retrieve the same response, which is useful for testing and automation.

---

## Key Takeaways

* GET is the default method used to retrieve resources
* HTTP Basic Authentication relies on the `Authorization` header
* GET parameters are included directly in the URL
* Browser DevTools and cURL are powerful tools for analyzing and reproducing GET requests.
