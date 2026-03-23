# XXE Prevention

XXE vulnerabilities occur primarily when unsafe XML input references external entities. Fortunately, prevention is relatively straightforward since these vulnerabilities stem mainly from outdated XML libraries rather than coding logic.

## Avoiding Outdated Components

XML parsing is typically handled by built-in libraries rather than custom code. If XXE vulnerabilities exist, outdated XML libraries are usually the culprit.

**Examples:**
- PHP's `libxml_disable_entity_loader` is deprecated since PHP 8.0.0
- Libraries like SOAP, SVG processors, and PDF processors may also parse XML unsafely
- Common package managers and editors (e.g., npm, VSCode) flag outdated components

**Best Practice:** Keep all XML libraries and parsing components updated.

> **Reference:** See [OWASP's XXE Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/) for vulnerable libraries and safe alternatives.

## Safe XML Configurations

Beyond updating libraries, implement these configurations:

- Disable custom DTD (Document Type Definition) references
- Disable external XML entity references
- Disable parameter entity processing
- Disable XInclude support
- Prevent entity reference loops
- Implement proper exception handling and disable runtime error display

> **Note:** These configurations provide defense-in-depth but shouldn't replace updates.

## Alternative Approaches

- **Use JSON/YAML** instead of XML when possible
- **Replace SOAP APIs** with REST APIs
- **Deploy Web Application Firewalls (WAFs)** as an additional layer

> **Important:** Never rely solely on WAFs. Always secure the backend.
