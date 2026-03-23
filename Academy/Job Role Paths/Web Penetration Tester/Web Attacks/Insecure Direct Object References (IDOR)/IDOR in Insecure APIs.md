# IDOR in Insecure APIs

## Overview

IDOR vulnerabilities extend beyond file access to include function calls and APIs. Exploiting these enables actions as other users: changing private information, resetting passwords, or making purchases with another user's payment data.

**Key distinction:**
- **Information Disclosure IDOR**: Read resources
- **Insecure Function Calls**: Execute functions/APIs as another user

## Identifying Insecure APIs

### Testing the Edit Profile Endpoint

The Employee Manager application's Edit Profile page updates user information (Full Name, Email, About Me).

**Intercepted PUT request to `/profile/api.php/profile/1`:**

```json
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

**Security concern:** Access control privileges (role, uid, uuid) are client-controlled, allowing potential manipulation without server-side validation.

## Exploitation Attempts

### Attempt 1: Change UID
Changing `uid` to another user's ID → **Response: `uid mismatch`**
- Server validates that request UID matches endpoint UID

### Attempt 2: Modify Another User's Details
Request to `/profile/api.php/profile/2` with matching `uid` → **Response: `uuid mismatch`**
- Server validates UUID matches the target user

### Attempt 3: Create New User
POST request to create user → **Response: `Creating new employees is for admins only`**
- Authorization based on `role=employee` cookie

### Attempt 4: Change Role to Admin
Modifying `role` parameter with invalid value → **Response: `Invalid role`**
- Role validation exists but enumeration is needed

## Next Steps

Test the API's GET requests for **IDOR Information Disclosure vulnerabilities**. Retrieving other users' details (including valid role names and UUIDs) could enable successful exploitation of the function call vulnerabilities.
