# Intercepting Web Requests

Now that we have set up our proxy, we can use it to intercept and manipulate various HTTP requests sent by the web application we are testing. We'll start by learning how to intercept web requests, change them, and then send them through to their intended destination.

## Intercepting Requests

### Burp

In Burp, navigate to the **Proxy** tab where request interception should be on by default. Toggle interception on/off in the **Intercept** sub-tab.

Once interception is enabled:
1. Start the pre-configured browser
2. Visit your target website
3. Return to Burp to see the intercepted request
4. Click **Forward** to send the request

> **Note:** All Firefox traffic will be intercepted. If you see unrelated requests, click **Forward** until you reach your target.

### ZAP

In ZAP, interception is **off by default** (green button on the top bar). Click the button or press `[CTRL+B]` to toggle interception.

Once enabled:
1. Start the pre-configured browser
2. Visit the target webpage
3. View the intercepted request in the top-right pane
4. Click the **Step** button to forward the request

#### Using ZAP HUD

ZAP's **Heads Up Display (HUD)** allows you to control features directly from the browser:
- Click the HUD button at the end of the top menu bar to enable it
- Use the second button on the left pane to toggle request interception
- Click **Step** to examine responses, or **Continue** to skip ahead

> **Note:** HUD may not work in all browser versions. On first use, you'll see a HUD tutorial—consider taking it for basics.

---

## Manipulating Intercepted Requests

Once intercepted, requests remain hanging until forwarded. You can examine and modify them before sending.

### Common Use Cases

Request interception helps test for vulnerabilities including:
- SQL injections
- Command injections
- Upload bypass
- Authentication bypass
- XSS / XXE
- Error handling & deserialization

### Example: Command Injection

Enable interception and set an IP value, then click **Ping**. The intercepted request looks like:

```http
POST /ping HTTP/1.1
Host: 94.237.62.138:32306
Content-Type: application/x-www-form-urlencoded

ip=1
```

Front-end validation typically restricts input to numbers. However, by intercepting, you can inject commands like `;ls;` to break the application logic if back-end validation is insufficient.

Changing `ip=1` to `ip=;ls;` and forwarding may reveal command execution—demonstrating how request manipulation exposes vulnerabilities.

> **Note:** This module covers proxy mechanics, not specific attacks. See other HTB Academy modules for detailed attack techniques.
