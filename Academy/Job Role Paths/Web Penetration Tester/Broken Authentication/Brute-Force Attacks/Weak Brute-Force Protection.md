# Weak Brute-Force Protection

After understanding different brute-force attacks on authentication mechanisms, this section discusses security mechanisms that thwart brute-forcing and how to potentially bypass them. Common protection mechanisms include rate limits and CAPTCHAs.

## Rate Limits

Rate limiting is a crucial technique in software development and network management that controls incoming request rates to a system or API. It prevents server overload, downtime, and brute-force attacks by enforcing a maximum request frequency within a specified timeframe.

When an attacker hits a rate limit during a brute-force attack, the attack is thwarted. Rate limits typically:
- Increment response time iteratively until attacks become infeasible
- Block attackers from accessing the service for a specified period

### Rate Limit Bypasses

Rate limits should only target attackers, not regular users. However, implementations often have weaknesses:

- **IP-based identification**: Middleboxes (reverse proxies, load balancers) mask the actual attacker IP
- **Header spoofing**: Attackers can manipulate headers like `X-Forwarded-For` to randomize their source and bypass rate limits (e.g., CVE-2020-35590)

## CAPTCHAs

A CAPTCHA (Completely Automated Public Turing test to tell Computers and Humans Apart) prevents bots from submitting requests by requiring human interaction. These challenges are easy for humans but difficult for bots—identifying distorted text, selecting objects, or solving puzzles.

**Benefits**: Prevent spam, fake accounts, and brute-force attacks on login pages.

**Limitations**: 
- Reduce usability for users with visual or cognitive impairments
- Increasingly vulnerable to automated solvers and AI-driven tools with advanced image/voice recognition

### CAPTCHA Security Issues

Never reveal CAPTCHA solutions in responses. Modern tools and browser extensions can automatically solve CAPTCHAs, making them less reliable as a sole defense mechanism.
