# Identifying Filters

As we have seen in the previous section, even if developers attempt to secure the web application against injections, it may still be exploitable if it was not securely coded. Another type of injection mitigation is utilizing blacklisted characters and words on the back-end to detect injection attempts and deny the request if any request contained them. Yet another layer on top of this is utilizing Web Application Firewalls (WAFs), which may have a broader scope and various methods of injection detection and prevent various other attacks like SQL injections or XSS attacks.

This section will look at a few examples of how command injections may be detected and blocked and how we can identify what is being blocked.

## Filter/WAF Detection

Let us start by visiting the web application in the exercise at the end of this section. We see the same Host Checker web application we have been exploiting, but now it has a few mitigations up its sleeve. When we try the previous operators we tested, like (`;`, `&&`, `||`), we get the error message **invalid input**.

> **Note:** The request includes headers like Host and User-Agent, with IP set to `127.0.0.1+%3b+whoami`. The response displays a Host Checker form with `127.0.0.1` entered and an **Invalid input** message.

This indicates that something we sent triggered a security mechanism in place that denied our request. This error message can be displayed in various ways:

- Displayed in the field where the output is displayed, this indicates it was detected and prevented by the PHP web application itself
- Displayed as a different page with information like our IP and our request, this may indicate that it was denied by a WAF

### Payload Analysis

Let us check the payload we sent:

```bash
127.0.0.1; whoami
```

Other than the IP (which we know is not blacklisted), we sent:

- A semi-colon character `;`
- A space character
- A `whoami` command

The web application either detected a blacklisted character or detected a blacklisted command, or both. We need to identify which and determine how to bypass each.

## Blacklisted Characters

A web application may have a list of blacklisted characters, and if the command contains them, it would deny the request. The PHP code may look something like the following:

```php
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}
```

If any character in the string we sent matches a character in the blacklist, our request is denied.

### Identifying Blacklisted Character

Before attempting to bypass the filter, we should identify which character caused the denied request. Let us reduce our request to one character at a time.

We know that the `127.0.0.1` payload works, so let us start by adding the semi-colon `127.0.0.1;`:

> **Note:** The request includes headers like Host and User-Agent, with IP set to `127.0.0.1%3b`. The response displays HTML for a Host Checker form with an **Invalid input** message.

We still get an invalid input error, meaning that a semi-colon is blacklisted. Let's see if all of the injection operators we discussed previously are blacklisted.
