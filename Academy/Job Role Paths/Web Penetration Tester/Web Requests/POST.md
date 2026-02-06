# POST

## Overview
POST requests are used when a web application needs to send data to the server without exposing it in the URL. This is common for actions such as authentication, file uploads, and submitting structured data. Unlike GET requests, POST places parameters in the **request body**.

---

## Why POST Is Used
POST requests provide several advantages over GET:

- **Reduced logging exposure**  
  Large payloads such as file uploads are not appended to the URL and are therefore not logged as part of the request path.

- **Binary data support**  
  Data in the request body can include binary content. Only parameter separators require encoding.

- **Larger payload capacity**  
  URLs have practical length limits (commonly around 2,000 characters), while POST bodies can carry significantly more data.

---

## Login Forms
Many web applications authenticate users through POST-based login forms.

When submitting credentials, the browser sends a POST request containing parameters such as:
```text
username=admin&password=admin
````

By inspecting the request in the browser’s Network tab, the request body can be viewed in raw form.

### Reproducing a Login Request with cURL

A POST request can be manually crafted using cURL:

```bash
curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/
```

If authentication is successful, the server responds with the authenticated page content rather than the login form.

### Handling Redirects

Some applications redirect users after login. Redirections can be followed using:

```bash
curl -L http://<SERVER_IP>:<PORT>/
```

---

## Authenticated Cookies

Upon successful authentication, servers often issue a session cookie using the `Set-Cookie` response header.

Example:

```text
Set-Cookie: PHPSESSID=<session_id>; path=/
```

This cookie allows the client to remain authenticated without resending credentials.

### Using Cookies with cURL

Cookies can be reused in subsequent requests:

```bash
curl -b 'PHPSESSID=<session_id>' http://<SERVER_IP>:<PORT>/
```

Alternatively, cookies can be set explicitly via headers:

```bash
curl -H 'Cookie: PHPSESSID=<session_id>' http://<SERVER_IP>:<PORT>/
```

Browsers allow manual inspection and modification of cookies through Developer Tools, enabling session reuse without logging in again.

---

## JSON Data in POST Requests

Modern web applications often exchange data using JSON instead of form-encoded parameters.

An example POST body:

```json
{"search":"london"}
```

Such requests require the appropriate content type:

```text
Content-Type: application/json
```

### Sending JSON with cURL

```bash
curl -X POST \
  -d '{"search":"london"}' \
  -H 'Content-Type: application/json' \
  -b 'PHPSESSID=<session_id>' \
  http://<SERVER_IP>:<PORT>/search.php
```

This allows direct interaction with backend endpoints without using the web interface.

---

## Using Fetch

Browser DevTools can generate equivalent JavaScript requests using the Fetch API. These requests can be executed directly in the browser console to reproduce the same behavior as cURL-based POST requests.

---

## Key Takeaways

* POST sends data in the request body instead of the URL
* It supports larger payloads and binary data
* Authentication often relies on POST requests and session cookies
* JSON-based POST requests are common in modern applications
* Direct interaction with backend endpoints is essential for efficient testing.
