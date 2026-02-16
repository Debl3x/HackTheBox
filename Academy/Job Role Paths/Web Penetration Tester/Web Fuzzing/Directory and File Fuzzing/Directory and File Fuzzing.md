# Directory and File Fuzzing

## Overview

Web applications often contain hidden directories and files not directly linked or visible to users. These resources may include sensitive information, backups, configuration files, or vulnerable application versions. Directory and file fuzzing systematically uncovers these hidden assets, revealing potential entry points for exploitation.

## Uncovering Hidden Assets

Hidden web resources typically contain:

- **Sensitive data**: Backup files, configuration settings, credentials, or logs
- **Outdated content**: Vulnerable versions of files or scripts
- **Development resources**: Test environments, staging sites, or admin panels
- **Hidden functionalities**: Undocumented features or vulnerable endpoints

These assets often lack robust security measures, making them prime targets for exploitation. Even non-vulnerable findings provide valuable intelligence about the application's architecture and technology stack.

## Wordlists

Wordlists are essential for fuzzing. They contain potential directory and file names compiled from:

- Web scraping for common naming patterns
- Public data breaches
- Known vulnerability databases

### SecLists Repository

[SecLists](https://github.com/danielmiessler/SecLists) is the most comprehensive wordlist collection.

**Location on pwnbox**: `/usr/share/seclists/` (lowercase)

**Commonly used wordlists**:

| Wordlist | Purpose |
|----------|---------|
| `Discovery/Web-Content/common.txt` | General-purpose starting point |
| `Discovery/Web-Content/directory-list-2.3-medium.txt` | Focused directory names |
| `Discovery/Web-Content/raft-large-directories.txt` | Extensive directory collection |
| `Discovery/Web-Content/big.txt` | Massive file and directory list |

## Fuzzing with ffuf

### How ffuf Works

1. **Wordlist**: Provide a list of potential names
2. **URL with FUZZ**: Use `FUZZ` as a placeholder in your URL
3. **Requests**: ffuf iterates through wordlist entries
4. **Response Analysis**: Filter results by status codes and content

### Directory Fuzzing

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ
```

**Parameters**:
- `-w`: Path to wordlist
- `-u`: Target URL with FUZZ placeholder

### File Fuzzing

Search for specific file extensions:

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://IP:PORT/directory/FUZZ -e .php,.html,.txt,.bak,.js -v
```

**Common extensions**:
- `.php`: Server-side scripts
- `.html`: Web pages
- `.txt`: Text/logs
- `.bak`: Backup files
- `.js`: JavaScript functionality

**Key flag**:
- `-e`: Specify file extensions to test

This approach can reveal exposed backups (e.g., `config.php.bak`) containing credentials or vulnerable legacy scripts.
