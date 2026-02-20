# Bypassing Blacklisted Commands

We have discussed various methods for bypassing single-character filters. However, there are different methods when it comes to bypassing blacklisted commands. A command blacklist usually consists of a set of words, and if we can obfuscate our commands and make them look different, we may be able to bypass the filters.

There are various methods of command obfuscation that vary in complexity. We will cover a few basic techniques that may enable us to change the look of our command to bypass filters manually.

## Commands Blacklist

A basic command blacklist filter in PHP would look like the following:

```php
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}
```

This code checks if any blacklisted words match the user input exactly. However, if we send a slightly different command, it may not get blocked. We can utilize various obfuscation techniques to execute our command without using the exact command word.

## Linux & Windows

One common obfuscation technique is inserting quotes within our command. Single quotes (`'`) and double quotes (`"`) are usually ignored by command shells like Bash or PowerShell.

**Examples:**

```bash
w'h'o'am'i
w"h"o"am"i
```

Both return the same result without being blocked.

> **Note:** Cannot mix quote types and the number of quotes must be even.

## Linux Only

You can insert backslash (`\`) or positional parameter (`$@`) characters within commands:

```bash
who$@ami
w\ho\am\i
```

Unlike quotes, the number of these characters does not have to be even.

## Windows Only

Windows allows the caret (`^`) character to be inserted without affecting the command:

```cmd
who^ami
```

In the next section, we will discuss more advanced techniques for command obfuscation and filter bypassing.
