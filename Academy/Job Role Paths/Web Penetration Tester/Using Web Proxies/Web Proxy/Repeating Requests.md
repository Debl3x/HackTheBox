# Repeating Requests

## Overview

In previous sections, we bypassed input validation to achieve command injection. Repeating the same process for different commands manually is inefficient—each command would require 5-6 steps. **Request repeating** allows us to resend previous requests, modify them quickly, and get responses without re-intercepting each time.

## Proxy History

### Burp Suite
Access HTTP history via **Proxy > HTTP History**. View requests with methods, status codes, URLs, and metadata.

### ZAP
Find history in the bottom **History pane** or main UI **History tab**.

**Both tools offer:**
- Filtering and sorting options
- WebSockets history (asynchronous updates and data fetching)

**Tip:** Burp shows both original and edited requests; ZAP shows only the final request sent.

## Repeating Requests

### Burp Suite
1. Select a request in history
2. Press `[CTRL+R]` to send to Repeater
3. Press `[CTRL+SHIFT+R]` to navigate to Repeater tab
4. Click **Send** to resend

**Tip:** Right-click a request and select **Change Request Method** to switch between POST/GET.

### ZAP Request Editor
1. Right-click a request in history
2. Select **Open/Resend with Request Editor**
3. Modify the request
4. Click **Send**

Use the **Method** dropdown to change HTTP methods.

**Tip:** Configure the display layout using the display buttons to show Request/Response as needed.

### ZAP HUD
1. Locate the request in the **History pane**
2. Click to open **Request Editor**
3. Choose:
    - **Replay in Console** (response in HUD window)
    - **Replay in Browser** (rendered response)

## Modifying Requests

All three tools allow inline request modification. Select, replace text, click **Send**, and receive instant output.

**Note:** Data is typically URL-encoded—essential for custom HTTP requests (covered in the next section).
