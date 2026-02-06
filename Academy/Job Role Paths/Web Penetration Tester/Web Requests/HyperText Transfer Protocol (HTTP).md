# HyperText Transfer Protocol (HTTP)

## Overview
HyperText Transfer Protocol (HTTP) is an application-layer protocol used to access resources on the World Wide Web. Most modern web and mobile applications rely on HTTP to communicate over the internet. The term *hypertext* refers to text that contains links to other resources and is easy for users to interpret.

HTTP follows a **client–server model**:
- The **client** sends a request for a resource.
- The **server** processes the request and returns a response.

By default, HTTP operates on **port 80**, although this can be changed through server configuration.

---

## URL Structure
HTTP resources are accessed through a **Uniform Resource Locator (URL)**, which provides detailed information about how and where to access a resource.

### URL Components
| Component | Example | Description |
|---------|--------|-------------|
| Scheme | `http://`, `https://` | Identifies the protocol used. Ends with `://`. |
| User Info | `admin:password@` | Optional credentials for authentication. |
| Host | `inlanefreight.com` | The hostname or IP address of the server. |
| Port | `:80` | Network port used. Defaults to 80 for HTTP and 443 for HTTPS. |
| Path | `/dashboard.php` | The resource path (file or directory). |
| Query String | `?login=true` | Parameters passed to the resource, separated by `&`. |
| Fragment | `#status` | Client-side reference to a section within the resource. |

Only the **scheme** and **host** are mandatory to make a valid request.

---

## HTTP Communication Flow
1. A user enters a domain name (e.g. `inlanefight.com`) into a browser.
2. The browser resolves the domain to an IP address using **DNS**.
   - The system first checks the local `/etc/hosts` file.
   - If no match is found, external DNS servers are queried.
3. Once the IP address is resolved, the browser sends an HTTP request (usually `GET`) to the server on port 80.
4. The server processes the request and returns an HTTP response.
5. The response includes:
   - The requested resource (e.g. `index.html`)
   - An HTTP status code (e.g. `200 OK`)
6. The browser renders the response content for the user.

When requesting `/`, servers typically return a default index file such as `index.html`.

---

## cURL
cURL (Client URL) is a command-line tool and library that supports HTTP and many other protocols. It is widely used for automation, scripting, and web penetration testing.

### Basic Usage
Send a simple HTTP request:
```bash
curl inlanefreight.com
````

This outputs the raw HTML response without rendering it.

### Downloading Files

* Save output using the remote filename:

```bash
curl -O inlanefreight.com/index.html
```

* Specify a custom output filename:

```bash
curl -o file.html inlanefreight.com/index.html
```

* Silent mode (no progress output):

```bash
curl -s -O inlanefreight.com/index.html
```

### Useful cURL Options

| Option                | Description                        |
| --------------------- | ---------------------------------- |
| `-d`, `--data`        | Send HTTP POST data                |
| `-i`, `--include`     | Include response headers           |
| `-o`, `--output`      | Write output to a specified file   |
| `-O`, `--remote-name` | Save output using remote filename  |
| `-s`, `--silent`      | Suppress progress output           |
| `-u`, `--user`        | Provide authentication credentials |
| `-A`, `--user-agent`  | Set a custom User-Agent            |
| `-v`, `--verbose`     | Enable verbose output              |

Additional documentation can be accessed using:

```bash
curl -h
curl --help all
man curl
```

---

## Key Takeaways

* HTTP is the foundation of web communication.
* URLs define how and where resources are accessed.
* DNS resolution is required before HTTP communication.
* cURL is a powerful tool for inspecting and automating HTTP requests.
