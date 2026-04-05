# Local File Inclusion (LFI)

Now that we understand what File Inclusion vulnerabilities are and how they occur, we can start learning how to exploit these vulnerabilities in different scenarios to read the content of local files on the back-end server.

## Basic LFI

The exercise at the end of this section shows a web app that allows users to set their language to either English or Spanish:

`http://<SERVER_IP>:<PORT>/`

If we select a language (e.g. Spanish), we see that the content text changes:

`http://<SERVER_IP>:<PORT>/index.php?language=es.php`

We also notice that the URL includes a `language` parameter set to `es.php`.

There are several ways the content could be changed to match the selected language:

- Pulling content from a different database table
- Loading an entirely different app view
- Including a local file/template (most common)

If the app is including a file, we may be able to change it to read another local file. Two common readable files are:

- Linux: `/etc/passwd`
- Windows: `C:\Windows\boot.ini`

So, we can try changing the parameter:

`http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd`

If the app is vulnerable, it will display the file contents and reveal back-end users.

## Path Traversal

In the earlier example, we read a file with an absolute path (`/etc/passwd`). This works if user input is used directly in `include()`:

```php
include($_GET['language']);
```

However, developers often append or prepend strings. For example:

```php
include("./languages/" . $_GET['language']);
```

Now if we request `/etc/passwd`, PHP tries:

`./languages//etc/passwd`

This usually fails:

`http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd`

The verbose error may reveal the final path passed to `include()`.

> **Note**
> PHP errors are enabled here for educational purposes. In production, errors should never be exposed. These attacks should still work without relying on error messages.

We can bypass directory restrictions with relative traversal (`../`).

If the full directory is `/var/www/html/languages/`, then:

- `../index.php` resolves to `/var/www/html/index.php`

So we can move up to root and then target the file:

`http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd`

This works regardless of the starting directory. Even if too many `../` are used, paths typically stay at root once reached.

> **Tip**
> Use the minimum number of `../` needed (cleaner for reports/exploits). Example: from `/var/www/html/`, use `../../../` to reach `/`.

## Filename Prefix

Sometimes input is appended after a prefix:

```php
include("lang_" . $_GET['language']);
```

Then `../../../etc/passwd` becomes:

`lang_../../../etc/passwd`

This is invalid:

`http://<SERVER_IP>:<PORT>/index.php?language=../../../etc/passwd`

One workaround is adding a leading slash:

`http://<SERVER_IP>:<PORT>/index.php?language=/../../../etc/passwd`

This may treat the prefix as a directory and allow traversal.

> **Note**
> This does not always work. If `lang_/` does not exist, the path may still fail. Prefixes can also break other inclusion techniques (e.g. PHP wrappers/filters or RFI).

## Appended Extensions

Another common pattern is appending an extension:

```php
include($_GET['language'] . ".php");
```

If we request `/etc/passwd`, the app tries to include:

`/etc/passwd.php`

Which usually does not exist:

`http://<SERVER_IP>:<PORT>/extension/index.php?language=/etc/passwd`

There are several bypass techniques for this, discussed in upcoming sections.

> **Exercise**
> Try reading any PHP file (e.g. `index.php`) through LFI. Check whether you get source code or rendered HTML.

## Second-Order Attacks

LFI can also appear as a **second-order attack**. This happens when a feature uses user-controlled data from storage (e.g. database) to build file paths.

Example:

`/profile/$username/avatar.png`

If a malicious username is stored (e.g. `../../../etc/passwd`), another feature (like avatar download) may include the wrong file.

In this case:

1. Poison a stored value (e.g. username during registration)
2. Trigger another function that uses that value in a file path

Developers may sanitize direct input (like `?page=`) but still trust DB values, making this possible.

Exploitation is similar to standard LFI. The key difference is identifying an indirect file-loading flow you can control.

> **Note**
> These techniques apply to LFI vulnerabilities regardless of back-end language or framework.
