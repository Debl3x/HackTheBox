# Parameter and Value Fuzzing

Building upon the discovery of hidden directories and files, we now delve into parameter and value fuzzing. This technique focuses on manipulating the parameters and their values within web requests to uncover vulnerabilities in how the application processes input.

Parameters are the messengers of the web, carrying vital information between your browser and the server that hosts the web application. They're like variables in programming, holding specific values that influence how the application behaves.

## GET Parameters: Openly Sharing Information

You'll often spot GET parameters right in the URL, following a question mark (`?`). Multiple parameters are strung together using ampersands (`&`). For example:

```
https://example.com/search?query=fuzzing&category=security
```

In this URL:
- `query` is a parameter with the value "fuzzing"
- `category` is another parameter with the value "security"

GET parameters are like postcards – their information is visible to anyone. They're primarily used for actions that don't change the server's state, like searching or filtering.

## POST Parameters: Behind-the-Scenes Communication

While GET parameters are like open postcards, POST parameters are more like sealed envelopes, carrying their information discreetly within the HTTP request body. They are not visible directly in the URL, making them the preferred method for transmitting sensitive data.

### How POST Requests Work

1. **Data Collection**: The information entered into form fields is gathered
2. **Encoding**: Data is encoded into a specific format:
    - `application/x-www-form-urlencoded`: Key-value pairs separated by ampersands
    - `multipart/form-data`: Used when submitting files with other data
3. **HTTP Request**: The encoded data is placed in the request body
4. **Server-Side Processing**: The server decodes and processes the data

### POST Request Example

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=your_username&password=your_password
```

## Why Parameters Matter for Fuzzing

Parameters are the gateways through which you interact with web applications. By manipulating their values, you can test how the application responds to different inputs:

- Altering a product ID in a shopping cart URL could reveal pricing errors
- Modifying hidden parameters might unlock hidden features
- Injecting malicious code could expose XSS or SQL Injection vulnerabilities

## Fuzzing with wenum

### Setup

```bash
alexsdb@htb[/htb]$ pipx install git+https://github.com/WebFuzzForge/wenum
alexsdb@htb[/htb]$ pipx runpip wenum install setuptools
```

### Testing GET Parameters

First, probe the endpoint with curl:

```bash
alexsdb@htb[/htb]$ curl http://IP:PORT/get.php
Invalid parameter value
x:
```

The response indicates the `x` parameter is missing. Let's add a value:

```bash
alexsdb@htb[/htb]$ curl http://IP:PORT/get.php?x=1
Invalid parameter value
x: 1
```

Now fuzz the parameter with wenum:

```bash
alexsdb@htb[/htb]$ wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "http://IP:PORT/get.php?x=FUZZ"
```

**Flags:**
- `-w`: Path to wordlist
- `--hc 404`: Hide 404 responses
- `x=FUZZ`: wenum replaces FUZZ with wordlist entries

Look for the response with a `200` status code – it indicates a valid parameter value:

```bash
alexsdb@htb[/htb]$ curl http://IP:PORT/get.php?x=OA...
HTB{...}
```

## Fuzzing with ffuf (POST Parameters)

Probe the POST endpoint:

```bash
alexsdb@htb[/htb]$ curl -d "" http://IP:PORT/post.php
Invalid parameter value
y:
```

The `-d` flag makes a POST request. The `y` parameter is expected but not provided.

Fuzz the POST parameter with ffuf:

```bash
alexsdb@htb[/htb]$ ffuf -u http://IP:PORT/post.php -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "y=FUZZ" \
  -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -mc 200 -v
```

**Key flags:**
- `-X POST`: Specify POST method
- `-d`: Data to send in request body
- `-mc 200`: Match response status code 200

Look for the valid parameter value in the results and validate it:

```bash
alexsdb@htb[/htb]$ curl -d "y=SU..." http://IP:PORT/post.php
HTB{...}
```

---

In real-world scenarios, valid parameter values might require more nuanced analysis beyond status codes, but this exercise demonstrates how to automate parameter fuzzing efficiently.
