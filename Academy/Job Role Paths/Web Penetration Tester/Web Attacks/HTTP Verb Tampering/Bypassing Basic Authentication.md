# Bypassing Basic Authentication

Exploiting HTTP Verb Tampering vulnerabilities is usually straightforward—we try alternate HTTP methods to see how the web server and application handle them. Automated scanners often miss vulnerabilities caused by insecure coding, as these require active testing.

The first type stems from **insecure web server configurations** and can allow bypassing HTTP Basic Authentication on certain pages.

## Identify

Starting the exercise, we encounter a basic File Manager web application where we can add files:

```
http://SERVER_IP:PORT/
```

However, clicking the red Reset button triggers an HTTP Basic Auth prompt—this functionality is restricted to authenticated users only, resulting in a **401 Unauthorized** response:

```
http://SERVER_IP:PORT/admin/reset.php
```

The `/admin` directory is entirely restricted. Let's bypass this with an HTTP Verb Tampering attack.

## Exploit

First, intercept the request in Burp Suite to identify the HTTP method. The page uses **GET requests**. 

Try sending a **POST request** instead: right-click the intercepted request and select *Change Request Method*. Both GET and POST are covered by authentication.

However, the **HEAD method** (identical to GET but without response body) might bypass authentication. Check accepted methods:

```bash
curl -i -X OPTIONS http://SERVER_IP:PORT/
```

**Response:**
```
Allow: POST,OPTIONS,HEAD,GET
```

The server accepts HEAD requests. Send a HEAD request to `/admin/reset.php`—you'll receive no login prompt and an empty response. The reset function executes successfully without credentials.

> **Tip:** Test other HTTP methods to identify additional bypass techniques.
