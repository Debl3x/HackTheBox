# Identifying IDORs

## URL Parameters & APIs

The first step in exploiting IDOR vulnerabilities is identifying Direct Object References. Look for URL parameters or APIs with object references (e.g., `?uid=1` or `?filename=file_1.pdf`) in HTTP requests.

**Basic exploitation:**
- Try incrementing values: `?uid=2`, `?filename=file_2.pdf`
- Use fuzzing to test thousands of variations
- Successful access to files not belonging to you indicates a vulnerability

## AJAX Calls

JavaScript frameworks may place all function calls client-side and disable certain roles in the UI. Admin or restricted functions may still exist in the front-end code.

**Example vulnerable AJAX call:**
```javascript
function changeUserPassword() {
    $.ajax({
        url: "change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success: function(result) { }
    });
}
```

Test these hidden functions for IDOR vulnerabilities even if they're not normally accessible.

## Hashing & Encoding

### Base64 Encoding
If parameters use encoding (e.g., `?filename=ZmlsZV8xMjMucGRm`), decode them, modify the reference, and re-encode:
- Decode: `ZmlsZV8xMjMucGRm` → `file_123.pdf`
- Modify: `file_124.pdf`
- Encode: `ZmlsZV8xMjQucGRm`

### Hashing
For hashed references like `?filename=c81e728d9d4c2f636f067f89cc14862c`, check the source code:
```javascript
data: {filename: CryptoJS.MD5('file_1.pdf').toString()}
```

Once you identify the hashing algorithm, calculate hashes for other filenames to test access.

## Compare User Roles

Register multiple accounts to compare HTTP requests and object references. This reveals how identifiers are calculated.

**Example:** If User1 accesses:
```json
{
  "type": "salary",
  "url": "/services/data/salaries/users/1",
  "Id": "1"
}
```

Try the same call as User2. If it succeeds without proper access control, it's an IDOR vulnerability.
