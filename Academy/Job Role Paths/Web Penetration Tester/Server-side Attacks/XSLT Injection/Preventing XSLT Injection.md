# Preventing XSLT Injection

## Overview
After discussing identification and exploitation of XSLT injection vulnerabilities, we now cover prevention strategies.

## Prevention Strategies

### Input Handling
XSLT injection can be prevented by ensuring user input is not inserted into XSL data before processing. If user-provided data must be included in the output:
- Implement proper sanitization and input validation
- Add user data to the XSL document **before** processing

### Output Format Encoding
**For HTML output:**
- HTML-encode user input before inserting it into XSL data
- Converts `<` to `&lt;` and `>` to `&gt;`
- Prevents injection of additional XSLT elements

### Additional Hardening Measures
- Run the XSLT processor with low-privilege process permissions
- Disable external functions by disabling PHP functions within XSLT
- Keep the XSLT library updated
