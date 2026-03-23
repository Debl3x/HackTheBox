# IDOR Prevention

We learned various ways to identify and exploit IDOR vulnerabilities in web pages, web functions, and API calls. By now, we should understand that IDOR vulnerabilities are mainly caused by improper access control on back-end servers. To prevent such vulnerabilities, we must build an object-level access control system and use secure references for objects when storing and calling them.

## Object-Level Access Control

An Access Control system should be at the core of any web application since it affects its entire design and structure. To properly control each area of the web application, its design must support the segmentation of roles and permissions in a centralized manner.

### Role-Based Access Control (RBAC)

User roles and permissions are vital to any access control system, fully realized in an RBAC system. To avoid exploiting IDOR vulnerabilities, map the RBAC to all objects and resources. The back-end server can allow or deny every request based on whether the requester's role has sufficient privileges.

Once RBAC is implemented, each user is assigned a role with certain privileges. Upon every request, their roles and privileges are tested to verify access. They can only access objects if they have the right to do so.

**Example implementation:**

```javascript
match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}
```

This approach maps the user token from the HTTP request to the RBAC to retrieve user roles and privileges. Access is only allowed if the user's uid matches the API endpoint or the user has admin role.

**Important Note:** Never store user roles in user details or cookies—these are user-controlled and can be manipulated. Instead, map privileges directly from the backend RBAC using the user's logged-in session token.

## Object Referencing

While the core IDOR issue is broken access control, direct object references enable enumeration and exploitation. Even with solid access control, never use clear text or simple patterns (e.g., `uid=1`). Always use strong, unique references like salted hashes or UUIDs.

### UUID Implementation

Use UUID V4 to generate strongly randomized IDs (e.g., `89c9b29b-d19f-4515-b2dd-abb6e693eb20`). Map this UUID to the object in the back-end database for quick cross-referencing.

**Key principles:**
- Generate hashes when objects are created and store them in the database
- Never calculate hashes on the front-end
- Create database maps for efficient object referencing

**Note:** UUIDs may let IDOR vulnerabilities go undetected since they're harder to test. Strong object referencing is always the second step after implementing solid access control. Some techniques (like repeating requests with different user sessions) work even with unique references if access control is broken.

## Conclusion

Implementing both object-level access control and secure object referencing provides strong protection against IDOR vulnerabilities.
