# Proxy Setup

Now that we have installed and started both tools, we'll learn how to use the most commonly used feature: **Web Proxy**.

We can set up these tools as a proxy for any application, such that all web requests would be routed through them so that we can manually examine what web requests an application is sending and receiving. This will enable us to understand better what the application is doing in the background and allows us to intercept and change these requests or reuse them with various changes to see how the application responds.

## Pre-Configured Browser

To use the tools as web proxies, we must configure our browser proxy settings to use them as the proxy or use the pre-configured browser. Both tools have a pre-configured browser that comes with pre-configured proxy settings and the CA certificates pre-installed, making starting a web penetration test very quick and easy.

### Burp Suite
In Burp's **Proxy > Intercept**, click on **Open Browser** to launch Burp's pre-configured browser, which automatically routes all web traffic through Burp.

### ZAP
In ZAP, click on the **Firefox browser icon** at the end of the top bar to open the pre-configured browser.

> **Note:** For this module, using the pre-configured browser should be sufficient.

## Manual Proxy Setup

In many cases, we may want to use a real browser for pentesting, like Firefox. To use Firefox with our web proxy tools, we must first configure it to use them as the proxy.

### Default Configuration
- Both **Burp** and **ZAP** use port **8080** by default
- Any available port can be used
- If a port is already in use, the proxy will fail to start with an error message

### Changing the Proxy Port
- **Burp:** `Proxy > Proxy settings > Proxy listeners`
- **ZAP:** `Tools > Options > Network > Local Servers/Proxies`
- Ensure Firefox is configured to use the same port

### Using FoxyProxy Extension

Instead of manually switching the proxy, use the **FoxyProxy** extension for easy proxy management.

**Installation:**
- Pre-installed in PwnBox
- Available on [Firefox Extensions Page](https://addons.mozilla.org/firefox/)

**Configuration:**
1. Click the FoxyProxy icon in Firefox's top bar
2. Select **Options**
3. Click **Add** in the left pane
4. Enter the following settings:
    - **IP:** `127.0.0.1`
    - **Port:** `8080`
    - **Name:** Burp or ZAP
5. Click **Save**

> **Note:** This configuration is already set up in PwnBox.

**Activation:**
Click the FoxyProxy icon and select **Burp/ZAP** to enable the proxy.

## Installing CA Certificate

Installing the web proxy's CA certificate is crucial for proper HTTPS traffic routing and to avoid repeated certificate warnings.

### Burp Certificate
1. Select Burp as your proxy in FoxyProxy
2. Browse to `http://burp`
3. Click on **CA Certificate** to download

### ZAP Certificate
1. Go to `Tools > Options > Network > Server Certificates`
2. Click **Save** to download the certificate
3. Optional: Generate a new certificate using the **Generate** button

### Installing in Firefox
1. Navigate to `about:preferences#privacy`
2. Scroll to the bottom and click **View Certificates**
3. Select the **Authorities** tab
4. Click **Import** and select the downloaded CA certificate
5. Check the following options:
    - ☑ Trust this CA to identify websites
    - ☑ Trust this CA to identify email users
6. Click **OK**

---

Once the certificate is installed and Firefox proxy is configured, all Firefox web traffic will route through your web proxy.
