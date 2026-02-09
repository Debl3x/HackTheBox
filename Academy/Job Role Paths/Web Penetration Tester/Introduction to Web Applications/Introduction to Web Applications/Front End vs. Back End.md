# Front End vs. Back End

We may have heard the terms **front end** and **back end** web development, or **Full Stack** web development, which refers to both. These terms are becoming synonymous with web application development, as they comprise the majority of the web development cycle. However, they are very different from each other, as each refers to one side of the web application and functions in different areas.

## Front End

The front end contains the user's components directly through their web browser (client-side). These components make up the source code of the web page and usually include HTML, CSS, and JavaScript, interpreted in real-time by browsers.

This includes everything the user sees and interacts with:
- **HTML**: The page's main structure and elements
- **CSS**: Design and animation of all elements
- **JavaScript**: Functionality of each part

Modern web applications must adapt to any screen size and work within any browser on any device. If the front end is not well optimized, it may make the entire application slow and unresponsive, causing users to blame the server or internet when the issue lies on the client-side.

**Related tasks:**
- Visual Concept Web Design
- User Interface (UI) Design
- User Experience (UX) Design

## Back End

The back end drives all core functionalities executed on the back end server. It is what we may never see or directly interact with, but without it, a website is just static pages.

**Main back end components:**

| Component | Description |
|-----------|-------------|
| Back end Servers | Hardware and OS hosting all components (Linux, Windows, Containers) |
| Web Servers | Handle HTTP requests (Apache, NGINX, IIS) |
| Databases | Store and retrieve data (MySQL, PostgreSQL, MongoDB, Oracle) |
| Development Frameworks | Build core applications (Laravel, ASP.NET, Spring, Django, Express) |

Each component can be hosted on isolated servers or containers (Docker) for logical separation and vulnerability mitigation.

**Main back end jobs:**
- Develop core logic and services
- Develop main code and functionalities
- Develop and maintain databases
- Develop and implement libraries
- Implement technical/business needs
- Implement main APIs for front end communications
- Integrate remote servers and cloud services
- Secure the application

## Securing Front/Back End

Even without access to back end code, applications can be exploited via injection attacks, command injections, or other techniques.

**Testing approaches:**
- **Whitebox Pentesting**: Code review with full access to source code
- **Blackbox Pentesting**: Testing without source code access

Some vulnerabilities like Local File Inclusion can expose back end source code, enabling deeper analysis.

### Top 20 Web Developer Mistakes

| No. | Mistake |
|-----|---------|
| 1 | Permitting Invalid Data to Enter the Database |
| 2 | Focusing on the System as a Whole |
| 3 | Establishing Personally Developed Security Methods |
| 4 | Treating Security as Your Last Step |
| 5 | Developing Plain Text Password Storage |
| 6 | Creating Weak Passwords |
| 7 | Storing Unencrypted Data in the Database |
| 8 | Depending Excessively on the Client Side |
| 9 | Being Too Optimistic |
| 10 | Permitting Variables via URL Path Name |
| 11 | Trusting Third-party Code |
| 12 | Hard-coding Backdoor Accounts |
| 13 | Unverified SQL Injections |
| 14 | Remote File Inclusions |
| 15 | Insecure Data Handling |
| 16 | Failing to Encrypt Data Properly |
| 17 | Not Using Secure Cryptographic Systems |
| 18 | Ignoring Layer 8 |
| 19 | Review User Actions |
| 20 | Web Application Firewall Misconfigurations |

### OWASP Top 10 Vulnerabilities

| No. | Vulnerability |
|-----|----------------|
| 1 | Broken Access Control |
| 2 | Cryptographic Failures |
| 3 | Injection |
| 4 | Insecure Design |
| 5 | Security Misconfiguration |
| 6 | Vulnerable and Outdated Components |
| 7 | Identification and Authentication Failures |
| 8 | Software and Data Integrity Failures |
| 9 | Security Logging and Monitoring Failures |
| 10 | Server-Side Request Forgery (SSRF) |

As penetration testers, we must competently identify, exploit, and explain these vulnerabilities for our clients.
