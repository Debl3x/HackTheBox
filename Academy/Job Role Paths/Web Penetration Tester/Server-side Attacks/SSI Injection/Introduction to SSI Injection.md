# Introduction to SSI Injection

Server-Side Includes (SSI) is a technology that web applications use to create dynamic content on HTML pages. SSI is supported by many popular web servers such as Apache and IIS. The use of SSI can often be inferred from the file extension. Typical file extensions include `.shtml`, `.shtm`, and `.stm`. However, web servers can be configured to support SSI directives in arbitrary file extensions.

## SSI Directives

SSI utilizes directives to add dynamically generated content to a static HTML page. An SSI directive has the following syntax:

```ssi
<!--#name param1="value1" param2="value" -->
```

### Common Directives

| Directive | Description | Syntax |
|-----------|-------------|--------|
| **printenv** | Prints environment variables | `<!--#printenv -->` |
| **config** | Changes SSI configuration (e.g., error messages) | `<!--#config errmsg="Error!" -->` |
| **echo** | Prints variable values (DOCUMENT_NAME, DOCUMENT_URI, LAST_MODIFIED, DATE_LOCAL) | `<!--#echo var="DOCUMENT_NAME" -->` |
| **exec** | Executes commands | `<!--#exec cmd="whoami" -->` |
| **include** | Includes files from web root | `<!--#include virtual="index.html" -->` |

## SSI Injection

SSI injection occurs when an attacker can inject SSI directives into a file that is subsequently served by the web server, resulting in the execution of the injected directives. This can happen through:

- **File upload vulnerabilities** – uploading files with malicious SSI directives to the web root
- **User input handling** – web applications writing unsanitized user input to files in the web root directory
