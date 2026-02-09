# Public Vulnerabilities

The most critical back-end component vulnerabilities are those exploitable externally, allowing attackers to take control of the back-end server without local access. These result from coding mistakes during development and range from basic to sophisticated vulnerabilities.

### Public CVE

Public web applications are frequently tested globally, uncovering numerous vulnerabilities. Most get patched and assigned a **CVE (Common Vulnerabilities and Exposures)** record with a severity score.

Penetration testers often create proof-of-concept exploits for public vulnerabilities, making them available for testing and educational purposes. **Searching for public exploits is the first step in web application testing.**

**Identifying the Web Application Version:**
- Check source code, configuration files, or version pages (e.g., `version.php`)
- For open-source applications, check the repository

**Finding Public Exploits:**
Once you identify the version, search for exploits in:
- Google search
- Exploit databases: Exploit DB, Rapid7 DB, Vulnerability Lab
- Example: https://www.rapid7.com/db/?q=wordpress&type=nexpose

**Priority Exploits:**
- CVSS score of 8-10
- Remote Code Execution (RCE)
- External component vulnerabilities (plugins, libraries)

### CVSS (Common Vulnerability Scoring System)

CVSS is an industry standard for assessing vulnerability severity, using metrics: Base, Temporal, and Environmental.

**CVSS V2.0 Ratings:**
| Severity | Base Score |
|----------|-----------|
| Low | 0.0-3.9 |
| Medium | 4.0-6.9 |
| High | 7.0-10.0 |

**CVSS V3.0 Ratings:**
| Severity | Base Score |
|----------|-----------|
| None | 0.0 |
| Low | 0.1-3.9 |
| Medium | 4.0-6.9 |
| High | 7.0-8.9 |
| Critical | 9.0-10.0 |

The **National Vulnerability Database (NVD)** provides CVSS scores and calculators for fine-tuning scores based on your environment.

### Back-End Server Vulnerabilities

Critical vulnerabilities exist in:
- **Web servers** (publicly accessible): Example: Shell-Shock affecting Apache servers pre-2014
- **Back-end servers & databases** (usually exploited post-compromise)

Back-end vulnerabilities require patching to prevent complete web application compromise.
