# Running SQLMap on an HTTP Request

SQLMap offers numerous options and switches to properly configure HTTP requests before usage. Common mistakes—such as forgetting cookies, overcomplicating commands, or improperly formatting POST data—can prevent successful SQLi detection and exploitation.

## Curl Commands

The easiest way to set up SQLMap requests is using the **Copy as cURL** feature from your browser's Developer Tools (Network panel). Simply:

1. Copy the cURL command from the browser
2. Replace `curl` with `sqlmap`
3. Paste into your terminal

**Example:**
```bash
sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0...' --compressed
```

## GET/POST Requests

**GET parameters** use the `-u/--url` option:
```bash
sqlmap -u 'http://www.example.com/?id=1'
```

**POST parameters** use the `--data` flag:
```bash
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

To test a specific parameter, use `-p uid` or mark it with `*`:
```bash
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```

## Full HTTP Requests

For complex requests with multiple headers and POST bodies, use the `-r` flag with a request file:

```bash
sqlmap -r req.txt
```

**Request file format (captured from Burp):**
```http
GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0)
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en;q=0.5
Connection: close
```

**Tip:** Mark injection points with `*` in the request file (e.g., `/?id=*`).

## Custom Headers and Options

**Cookie/Session:**
```bash
sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

**User-Agent:**
```bash
sqlmap ... -A 'Mozilla/5.0'
sqlmap ... --random-agent  # Use random browser User-Agent
sqlmap ... --mobile         # Imitate smartphone
```

**HTTP Method:**
```bash
sqlmap -u www.target.com --data='id=1' --method PUT
```

## JSON and XML Requests

SQLMap supports JSON and XML formatted POST bodies:

```bash
sqlmap -r req.txt
```

**JSON example:**
```json
{
    "data": [{
        "type": "articles",
        "id": "1",
        "attributes": {
            "title": "Example JSON"
        }
    }]
}
```

SQLMap will detect and prompt you to process these formats automatically.
