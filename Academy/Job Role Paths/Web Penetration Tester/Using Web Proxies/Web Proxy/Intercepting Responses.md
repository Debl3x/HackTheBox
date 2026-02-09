# Intercepting Responses

In some instances, we may need to intercept HTTP responses from the server before they reach the browser. This is useful for changing how web pages render—enabling disabled fields or revealing hidden fields—which aids penetration testing.

## Burp

To enable response interception in Burp:

1. Go to **Proxy > Proxy settings**
2. Enable **Intercept Response** under Response interception rules
3. Refresh the page with `[CTRL+SHIFT+R]` to force a full refresh
4. Forward the intercepted request to view the response

### Example: Modifying Input Validation

Change `type="number"` to `type="text"` and `maxlength="3"` to `maxlength="100"`:

```html
<input type="text" id="ip" name="ip" min="1" max="255" maxlength="100"
    oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
    required>
```

This allows any value input instead of just numeric values.

## ZAP

In ZAP, click **Step** to send the request and automatically intercept the response. Make your changes and click **Continue**.

### Quick Enable/Show Features

ZAP HUD's lightbulb icon (third button) enables disabled fields or shows hidden fields without interception. Use the **Show/Enable** button to modify form elements directly.

### Additional Features

- **Comments button**: Displays HTML comments by clicking the `+` button and selecting "Comments"
- **Response modification rules**: Similar to Burp's unhide hidden form fields feature

---

**Next**: Learn to automate response modifications automatically without manual interception.
