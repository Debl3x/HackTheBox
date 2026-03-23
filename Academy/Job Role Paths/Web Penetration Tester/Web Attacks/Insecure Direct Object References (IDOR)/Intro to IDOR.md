# Intro to IDOR

## Overview

Insecure Direct Object References (IDOR) vulnerabilities are among the most common web vulnerabilities and can significantly impact vulnerable web applications. They occur when a web application exposes direct references to objects (files, database resources) that end-users can control, potentially accessing other similar objects without proper authorization.

**Key Point:** A lack of solid access control systems is the primary cause, not the direct reference itself.

## What Makes an IDOR Vulnerability

### The Problem
Simply exposing direct object references isn't inherently vulnerable—the real issue is weak access control. Many applications restrict access at the front-end only, assuming users won't access restricted URLs directly.

**Example:**
- User accesses: `download.php?file_id=123`
- Attacker tries: `download.php?file_id=124`
- If no back-end validation exists, the file may be accessible

### Why It's Widespread
- Implementing proper access control (like RBAC) is challenging
- Many developers neglect back-end validation
- Automating detection is difficult
- Vulnerabilities often go unnoticed until production

IDOR/Access Control issues exist even in major applications (Facebook, Instagram, Twitter).

## Impact of IDOR Vulnerabilities

### Information Disclosure
- Accessing private files and resources of other users
- Exposure of personal data or credit card information

### Data Manipulation
- Modifying or deleting other users' data
- Potential account takeover

### Privilege Escalation
- **IDOR Insecure Function Calls:** Calling admin-only functions with standard user privileges
- Unauthorized operations: password changes, role assignment
- Complete application compromise

### Exploitation Process
Attackers identify direct references (database IDs, URL parameters) and test patterns to extract or modify arbitrary user data.
