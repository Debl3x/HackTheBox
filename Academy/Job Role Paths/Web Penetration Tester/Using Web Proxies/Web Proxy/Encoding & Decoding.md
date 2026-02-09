# Encoding/Decoding

As we modify and send custom HTTP requests, we may need to perform various encoding and decoding operations to interact with the web server properly. Both Burp and ZAP have built-in encoders to help us quickly encode and decode different text types.

## URL Encoding

Ensure that request data is URL-encoded and headers are correctly set, otherwise we may receive server errors. Key characters that need encoding:

| Character | Issue if not encoded |
|-----------|----------------------|
| Space | May indicate end of request data |
| `&` | Interpreted as parameter delimiter |
| `#` | Interpreted as fragment identifier |

**In Burp Repeater:**
- Select text → right-click → **Convert Selection > URL > URL-encode key characters**
- Or use `[CTRL+U]`
- Enable automatic encoding as you type via right-click menu

**In ZAP:** Automatically URL-encodes request data in the background.

Other URL-encoding variants (Full URL-Encoding, Unicode URL-encoding) are useful for requests with many special characters.

## Decoding

Web applications commonly encode data, so we must quickly decode it to examine the original text. Back-end servers may also expect specific encoding formats.

**Common encoding types supported:**
- HTML
- Unicode
- Base64
- ASCII hex

**Access encoders:**
- **Burp:** Decoder tab or Inspector tool (in Proxy/Repeater)
- **ZAP:** Encoder/Decoder/Hash `[CTRL+E]`

### Example: Decoding Base64

Given encoded cookie: `eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=`

Select **Decode as > Base64** to get: `{"username":"guest", "is_admin":false}`

> **Tip:** In ZAP, use "Add New Tab" to create customized encoder/decoder combinations.

## Encoding

Modify decoded values for penetration testing. Example: Change `guest` to `admin` and `false` to `true`, then re-encode using the original method (base64).

Result: `{"username":"admin", "is_admin":true}` → Base64 encoded for use in requests.

> **Tip:** In Burp Decoder, select a new encoder in the output pane to re-encode immediately. In ZAP, copy output and paste into the input field.

Use the encoded string in Burp Repeater or ZAP Request Editor for effective web penetration testing.
