# Filtering Fuzzing Output

Web fuzzing tools like gobuster, ffuf, and wfuzz generate vast amounts of data. These tools offer powerful filtering mechanisms to streamline analysis and focus on relevant results.

## Gobuster

Gobuster filtering options (available in dir mode):

| Flag | Description | Example |
|------|-------------|---------|
| `-s` | Include specific status codes | `-s 200,301` for OK and redirects |
| `-b` | Exclude specific status codes | `-b 404` to hide not found errors |
| `--exclude-length` | Exclude by content length | `--exclude-length 0,404` |

**Example:**
```bash
gobuster dir -u http://example.com/ -w wordlist.txt -s 200,301 --exclude-length 0
```

## FFUF

FFUF offers highly customizable filtering:

| Flag | Purpose | Example |
|------|---------|---------|
| `-mc` | Match status codes | `-mc 200` or `-mc 200-299` |
| `-fc` | Filter status codes | `-fc 404,401,302` |
| `-fs` | Filter by size | `-fs 0-1023` for responses under 1KB |
| `-ms` | Match size | `-ms 3456` for exact size |
| `-fw` | Filter word count | `-fw 219` |
| `-mw` | Match word count | `-mw 5-10` |
| `-fl` | Filter line count | `-fl 10` |
| `-ml` | Match line count | `-ml 20` |
| `-mt` | Match response time | `-mt >500` for TTFB > 500ms |

**Examples:**
```bash
ffuf -u http://example.com/FUZZ -w wordlist.txt -mc 200 -fw 427 -ms >500
ffuf -u http://example.com/FUZZ -w wordlist.txt -fc 404,401,302
ffuf -u http://example.com/FUZZ.bak -w wordlist.txt -fs 0-10239 -ms 10240-102400
ffuf -u http://example.com/FUZZ -w wordlist.txt -mt >500
```

## wfuzz

wfuzz filtering options:

| Flag | Purpose | Example |
|------|---------|---------|
| `--hc` | Hide status codes | `--hc 400` |
| `--sc` | Show status codes | `--sc 200` |
| `--hl` | Hide by line count | `--hl` with high value |
| `--sl` | Show by line count | `--sl` |
| `--hw` | Hide by word count | `--hw` |
| `--sw` | Show by word count | `--sw 5-10` |
| `--hs` | Hide by size | `--hs 10000` |
| `--ss` | Show by size | `--ss` |
| `--hr` | Hide by regex | `--hr "Internal Server Error"` |
| `--sr` | Show by regex | `--sr "admin"` |

**Examples:**
```bash
wfuzz -w wordlist.txt --sc 200,301,302 -u https://example.com/FUZZ
wfuzz -w wordlist.txt --hc 404,400,500 -u https://example.com/FUZZ
wfuzz -w wordlist.txt --sw 5-10 -u https://example.com/FUZZ
wfuzz -w wordlist.txt --sr "admin\|password" -u https://example.com/FUZZ
```

## Feroxbuster

Feroxbuster filtering at request and response levels:

| Flag | Purpose | Example |
|------|---------|---------|
| `--dont-scan` | Exclude URLs from scanning | `--dont-scan /uploads` |
| `-S, --filter-size` | Exclude by size | `-S 1024` |
| `-X, --filter-regex` | Exclude by regex | `-X "Access Denied"` |
| `-W, --filter-words` | Exclude by word count | `-W 0-10` |
| `-N, --filter-lines` | Exclude by line count | `-N 50-` |
| `-C, --filter-status` | Exclude status codes | `-C 404,500` |
| `--filter-similar-to` | Exclude similar pages | `--filter-similar-to error.html` |
| `-s, --status-codes` | Include status codes | `-s 200,204,301,302` |

**Example:**
```bash
feroxbuster --url http://example.com -w wordlist.txt -s 200 -S 10240 -X "error"
```

## Why Filtering Matters

By default, ffuf matches: `200-299, 301, 302, 307, 401, 403, 405, 500` to minimize noise from 404 responses. Without filtering, results become overwhelmed with not-found errors, making real findings difficult to identify.
