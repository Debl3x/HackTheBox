# Intro to Web Proxies

## Overview

Modern web and mobile applications rely on continuous back-end server connections for data exchange. Web Application Penetration Testing primarily focuses on testing these back-end communications, requiring **Web Proxies** to capture and manipulate requests.

## What Are Web Proxies?

Web proxies are specialized tools positioned between a browser/mobile application and back-end servers. They function as man-in-the-middle (MITM) tools to:

- Capture and view all web requests
- Operate on web ports (HTTP/80, HTTPS/443)
- Intercept and modify requests for testing purposes
- Analyze server responses

Unlike network sniffing tools like Wireshark, web proxies focus specifically on web traffic.

## Common Uses

- Web application vulnerability scanning
- Web fuzzing
- Web crawling
- Web application mapping
- Web request analysis
- Web configuration testing
- Code reviews

## Burp Suite

**Burp Suite** is the most popular web proxy tool for penetration testing, featuring an excellent UI and built-in Chromium browser.

**Community Edition:** Free and powerful for most pentests

**Paid Features:**
- Active web app scanner
- Fast Burp Intruder
- Extended extensions support

> **Tip:** Educational/business email holders can request a free Burp Pro trial.

## OWASP Zed Attack Proxy (ZAP)

**ZAP** is a free, open-source alternative maintained by OWASP with no paid-only features.

**Advantages:**
- Completely free and open-source
- No throttling or scan limitations
- Growing feature parity with Burp Pro through community contributions

## Conclusion

Both tools offer similar learning curves and complementary strengths. Choose based on your budget, pentesting scope, and feature requirements.
