# Bypassing Space Filters

There are numerous ways to detect injection attempts, and multiple methods to bypass these detections. This guide demonstrates detection concepts and bypassing techniques using Linux as an example, helping you understand how to utilize these bypasses and eventually prevent them.

## Bypass Blacklisted Operators

Most injection operators are blacklisted, but the new-line character (`%0a`) is often not blacklisted as it may be needed in the payload itself. Since the new-line character works in appending commands on both Linux and Windows, it can be used as an injection operator.

## Bypass Blacklisted Spaces

Once you have a working injection operator, the next hurdle is the space character, which is commonly blacklisted—especially when input shouldn't contain spaces, like IP addresses.

### Using Tabs

Replace spaces with tabs (`%09`). Both Linux and Windows accept commands with tabs between arguments:

```
127.0.0.1%0a%09whoami
```

### Using $IFS

Use the Linux environment variable `$IFS` (which defaults to space and tab):

```
127.0.0.1%0a${IFS}whoami
```

### Using Brace Expansion

Bash Brace Expansion automatically adds spaces between arguments in braces:

```bash
{ls,-la}
```

This can be applied to command injection:

```
127.0.0.1%0a{whoami}
```

---

For additional space filter bypass techniques, refer to PayloadsAllTheThings documentation on writing commands without spaces.
