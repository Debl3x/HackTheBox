# Introduction to Web Attacks

As web applications become increasingly common in business operations, protecting them against malicious attacks is critical. Modern web applications are complex and face sophisticated attack techniques, creating a vast attack surface. Web attacks are the most common threat to companies today, making web application security a top priority for IT departments.

Compromising external-facing web applications can lead to:
- Internal network breach
- Stolen assets or disrupted services
- Financial disaster

Even companies without external web applications face risks through internal applications and API endpoints, which are vulnerable to the same attacks.

This module covers three critical web attacks found in any web application that can lead to compromise. We'll discuss detection, exploitation, and prevention for each.

## Web Attacks Overview

### HTTP Verb Tampering

Exploits web servers that accept multiple HTTP verbs and methods. Attackers send unexpected HTTP requests to:
- Bypass authorization mechanisms
- Circumvent security controls

### Insecure Direct Object References (IDOR)

One of the most common web vulnerabilities. Occurs when web applications lack solid access control mechanisms. Attackers can:
- Access sequential file IDs or user numbers
- Retrieve other users' data by guessing or calculating identifiers

### XML External Entity (XXE) Injection

Affects web applications that process XML data via outdated XML libraries. Attackers can:
- Disclose local files from the back-end server
- Extract sensitive information (credentials, source code)
- Enable Whitebox Penetration Testing
- Steal server credentials for remote code execution

Let's explore the first attack in the next section.
