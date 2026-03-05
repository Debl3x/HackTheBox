# Further Session Attacks

After discussing how to attack session tokens, we will now examine two attack vectors against flawed handling of session tokens in web applications.

> **Note:** More advanced session attacks, such as Session Puzzling, are covered in the [Abusing HTTP Misconfigurations](../Abusing%20HTTP%20Misconfigurations.md) module.

## Session Fixation

Session Fixation is an attack that enables an attacker to obtain a victim's valid session. A vulnerable web application does not assign a new session token after successful authentication. If an attacker coerces the victim into using an attacker-chosen session token, they can steal the victim's session and access their account.

### Attack Scenario

Assume a vulnerable web application that:
- Uses a session token in the HTTP cookie `session`
- Sets the session cookie from the `sid` GET parameter

**Attack flow:**

1. Attacker authenticates and obtains a valid session token (e.g., `a1b2c3d4e5f6`)
2. Attacker logs out and sends the victim a malicious link: `http://vulnerable.htb/?sid=a1b2c3d4e5f6`
3. Victim clicks the link; the application sets: `Set-Cookie: session=a1b2c3d4e5f6`
4. Victim authenticates, sending the attacker-provided session cookie
5. Application accepts the session without generating a new token
6. Attacker hijacks the victim's session using the known token

### Prevention

A web application must assign a new randomly generated session token after successful authentication.

## Improper Session Timeout

A web application must define proper session timeouts. After the timeout interval expires, the session token becomes invalid. Without a timeout, hijacked sessions remain valid indefinitely.

### Best Practices

- **Timeout duration** depends on business requirements
- **Sensitive applications** (health data): minutes
- **General applications** (social media): multiple hours
