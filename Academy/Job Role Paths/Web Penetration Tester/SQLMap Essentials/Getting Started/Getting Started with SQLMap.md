# Getting Started with SQLMap

Upon starting SQLMap, new users typically begin with the help message. Two levels of help are available:

## Basic Help
Shows essential options (switch `-h`):

```bash
$ sqlmap -h
    ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9#stable}
|_ -| . ["]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

Target:
  -u URL, --url=URL     Target URL (e.g. "http://www.site.com/vuln.php?id=1")
  -g GOOGLEDORK         Process Google dork results as target URLs
```

## Advanced Help
Shows all options (switch `-hh`). Includes additional options like direct database connection, request configuration, and more.

For complete documentation, see the [official SQLMap wiki](http://sqlmap.org).

## Basic Scenario

A penetration tester finds a web page accepting user input via GET parameter (e.g., `id`) and tests for SQL injection vulnerabilities.

**Vulnerable PHP code example:**

```php
$link = mysqli_connect($host, $username, $password, $database, 3306);
$sql = "SELECT * FROM users WHERE id = " . $_GET["id"] . " LIMIT 0, 1";
$result = mysqli_query($link, $sql);
if (!$result)
    die("<b>SQL error:</b> ". mysqli_error($link) . "<br>\n");
```

### Running SQLMap

Command to test `http://www.example.com/vuln.php?id=1`:

```bash
$ sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
```

**Key output:**
- Identifies injection point(s) found
- Shows vulnerability type (boolean-based blind, error-based, time-based blind, UNION query)
- Provides payloads for exploitation
- Detects backend DBMS and technology stack

**Options used:**
- `-u`: Target URL
- `--batch`: Skip user input prompts, use defaults
