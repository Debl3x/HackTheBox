# Intro to File Inclusions

Many modern back-end languages, such as PHP, JavaScript, or Java, use HTTP parameters to specify what is shown on a web page. This allows building dynamic web pages, reduces script size, and simplifies code.

In such cases, parameters are used to specify which resource is shown on the page. If these functionalities are not securely coded, an attacker may manipulate these parameters to display the content of any local file on the hosting server, leading to a **Local File Inclusion (LFI)** vulnerability.

## Local File Inclusion (LFI)

The most common place to find LFI is within templating engines. To keep most of a web application consistent when navigating between pages, a templating engine displays common static parts (such as the header, navigation bar, and footer), then dynamically loads changing content.

Otherwise, every page on the server would need modification whenever static parts are updated. This is why we often see a parameter like `/index.php?page=about`, where `index.php` sets static content and then loads dynamic content based on the parameter (e.g., `about.php`).

Since we control the `about` portion of the request, it may be possible to make the web application load other files and display them.

LFI vulnerabilities can lead to source code disclosure, sensitive data exposure, and even remote code execution under certain conditions. Leaked source code may reveal additional vulnerabilities, while leaked sensitive data may expose credentials, keys, and internal server details.

## Examples of Vulnerable Code

File Inclusion vulnerabilities can occur across many web servers and frameworks, including PHP, NodeJS, Java, and .NET. Each has a different implementation, but they all share one behavior: loading a file from a specified path.

For example, a page may use a `?language` parameter. If changing language modifies the loaded directory (e.g., `/en/` or `/es/`), and the path is user-controlled, it may be exploitable to read arbitrary files and potentially reach remote code execution.

### PHP

In PHP, `include()` can load a local or remote file. If its path comes from unsanitized user input, the code becomes vulnerable:

```php
if (isset($_GET['language'])) {
        include($_GET['language']);
}
```

The `language` parameter is directly passed to `include()`, so any supplied path may be loaded. Similar risk exists with:

- `include_once()`
- `require()`
- `require_once()`
- `file_get_contents()`

> **Note:** This module mostly focuses on PHP applications on Linux back-end servers, but most techniques also apply to other frameworks and languages.

### NodeJS

NodeJS servers may also load content from HTTP parameters:

```javascript
if (req.query.language) {
        fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
                res.write(data);
        });
}
```

Here, URL input is used by `readFile()`, and file content is written into the response.

Another example uses Express `render()`:

```js
app.get("/about/:language", function (req, res) {
        res.render(`/${req.params.language}/about.html`);
});
```

In this case, the parameter is in the URL path (e.g., `/about/en` or `/about/es`) and directly influences which file is rendered.

### Java

Java web applications can show the same pattern:

```jsp
<c:if test="${not empty param.language}">
        <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>
```

The `include` function can take a file or URL and render it in the front-end template.

Another example is `import`:

```jsp
<c:import url="<%= request.getParameter('language') %>"/>
```

### .NET

In .NET, `Response.WriteFile` can be vulnerable if fed by unsanitized user input:

```cs
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
        <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %>
}
```

Other examples include:

```cs
@Html.Partial(HttpContext.Request.Query['language'])
```

```cs
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->
```

## Read vs Execute

Some inclusion functions only read file content; others may execute files. Some also support remote URLs, while others only load local files.

### Function Behavior Table

| Function | Read Content | Execute | Remote URL |
|---|---|---|---|
| **PHP** ||||
| `include()` / `include_once()` | ✅ | ✅ | ✅ |
| `require()` / `require_once()` | ✅ | ✅ | ❌ |
| `file_get_contents()` | ✅ | ❌ | ✅ |
| `fopen()` / `file()` | ✅ | ❌ | ❌ |
| **NodeJS** ||||
| `fs.readFile()` | ✅ | ❌ | ❌ |
| `fs.sendFile()` | ✅ | ❌ | ❌ |
| `res.render()` | ✅ | ✅ | ❌ |
| **Java** ||||
| `include` | ✅ | ❌ | ❌ |
| `import` | ✅ | ✅ | ✅ |
| **.NET** ||||
| `@Html.Partial()` | ✅ | ❌ | ❌ |
| `@Html.RemotePartial()` | ✅ | ❌ | ✅ |
| `Response.WriteFile()` | ✅ | ❌ | ❌ |
| `include` | ✅ | ✅ | ✅ |

This distinction is important: executing files may enable function execution and eventually code execution, while read-only behavior usually exposes source code only.

In white-box exercises or code audits, understanding these function behaviors helps identify potential File Inclusion vulnerabilities, especially where user-controlled input is passed to them.

In all cases, File Inclusion vulnerabilities are critical and may eventually lead to full compromise of the back-end server. Even source code disclosure alone may reveal database keys, admin credentials, or other sensitive information.
