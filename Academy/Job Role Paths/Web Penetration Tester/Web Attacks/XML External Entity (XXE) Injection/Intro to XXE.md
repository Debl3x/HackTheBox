# Intro to XXE

XML External Entity (XXE) Injection vulnerabilities occur when XML data is taken from a user-controlled input without properly sanitizing or safely parsing it, which may allow us to use XML features to perform malicious actions. XXE vulnerabilities can cause considerable damage to a web application and its back-end server, from disclosing sensitive files to shutting the back-end server down, which is why it is considered one of the Top 10 Web Security Risks by OWAS.

## XML

Extensible Markup Language (XML) is a common markup language (similar to HTML and SGML) designed for flexible transfer and storage of data and documents in various types of applications. XML is not focused on displaying data but mostly on storing documents' data and representing data structures. XML documents are formed of element trees, where each element is essentially denoted by a tag, and the first element is called the root element, while other elements are child elements.

Here we see a basic example of an XML document representing an e-mail document structure:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<email>
    <date>01-01-2022</date>
    <time>10:00 am UTC</time>
    <sender>john@inlanefreight.com</sender>
    <recipients>
        <to>HR@inlanefreight.com</to>
        <cc>
                <to>billing@inlanefreight.com</to>
                <to>payslips@inlanefreight.com</to>
        </cc>
    </recipients>
    <body>
    Hello,
            Kindly share with me the invoice for the payment made on January 1, 2022.
    Regards,
    John
    </body> 
</email>
```

The above example shows some of the key elements of an XML document:

| Key | Definition | Example |
|-----|-----------|---------|
| Tag | The keys of an XML document, usually wrapped with `</>` characters | `<date>` |
| Entity | XML variables, usually wrapped with `&/;` characters | `&lt;` |
| Element | The root element or any of its child elements, and its value is stored between start and end tags | `<date>01-01-2022</date>` |
| Attribute | Optional specifications for any element stored in the tags | `version="1.0"` |
| Declaration | Usually the first line of an XML document, defines XML version and encoding | `<?xml version="1.0" encoding="UTF-8"?>` |

Some characters are used as part of XML structure (`<`, `>`, `&`, `"`). To use them in XML documents, replace them with entity references (`&lt;`, `&gt;`, `&amp;`, `&quot;`). Comments are written between `<!--` and `-->`.

## XML DTD

XML Document Type Definition (DTD) allows validation of an XML document against a pre-defined structure. The structure can be defined within the document or in an external file:

```xml
<!DOCTYPE email [
    <!ELEMENT email (date, time, sender, recipients, body)>
    <!ELEMENT recipients (to, cc?)>
    <!ELEMENT cc (to*)>
    <!ELEMENT date (#PCDATA)>
    <!ELEMENT time (#PCDATA)>
    <!ELEMENT sender (#PCDATA)>
    <!ELEMENT to  (#PCDATA)>
    <!ELEMENT body (#PCDATA)>
]>
```

The DTD can be placed within the XML document or in an external file and referenced with the `SYSTEM` keyword:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">
```

DTD can also be referenced through a URL:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">
```

## XML Entities

Custom entities (XML variables) can be defined in DTDs using the `ENTITY` keyword:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
    <!ENTITY company "Inlane Freight">
]>
```

Entities are referenced between `&` and `;` (e.g., `&company;`) and are replaced with their values by the XML parser.

External XML Entities can be referenced using the `SYSTEM` keyword:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
    <!ENTITY company SYSTEM "http://localhost/company.txt">
    <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```

> **Note:** The `PUBLIC` keyword can be used instead of `SYSTEM` for loading external resources, often used with publicly declared entities and standards. This module uses `SYSTEM`, but both work in most cases.

When the XML file is parsed server-side (in SOAP APIs or web forms), an external entity can reference a file stored on the back-end server, which may be disclosed when the entity is referenced.
