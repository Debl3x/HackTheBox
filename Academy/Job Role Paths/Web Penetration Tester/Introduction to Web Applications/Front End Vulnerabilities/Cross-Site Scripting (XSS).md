# Cross-Site Scripting (XSS)

HTML Injection vulnerabilities can be exploited to perform Cross-Site Scripting (XSS) attacks by injecting JavaScript code executed on the client-side. This enables attackers to potentially gain access to victim accounts or machines. While XSS is similar to HTML Injection, it involves injecting JavaScript for advanced client-side attacks rather than just HTML code.

## Types of XSS

| Type | Description |
|------|-------------|
| **Reflected XSS** | User input is displayed after processing (e.g., search results, error messages) |
| **Stored XSS** | User input is stored in the backend database and displayed upon retrieval (e.g., posts, comments) |
| **DOM XSS** | User input is directly shown in the browser and written to an HTML DOM object (e.g., username, page title) |

## Example Attack

Without input sanitization, a page vulnerable to HTML Injection can also be exploited for XSS. The following DOM XSS payload retrieves the current user's cookie:

```javascript
#"><img src=/ onerror=alert(document.cookie)>
```

When executed, this payload accesses the HTML document tree and displays the cookie value in an alert popup. The browser processes the input as a new DOM, executing the JavaScript code.

## Impact

Attackers can leverage XSS to:
- Steal cookie sessions and authenticate as the victim
- Perform various attacks against web application users
- Compromise user accounts and data

XSS is covered in-depth in later modules.
