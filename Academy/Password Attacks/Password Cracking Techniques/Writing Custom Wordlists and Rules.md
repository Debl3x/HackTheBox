# Writing Custom Wordlists and Rules

Many users create their passwords based on simplicity rather than security. To mitigate this human tendency (which often undermines security measures), password policies can be implemented on systems to enforce specific password requirements. For instance, a system might enforce the inclusion of uppercase letters, special characters, and numbers. Most password policies mandate a minimum length—typically eight characters—and require at least one character from each specified category.

We can use Hashcat to combine lists of potential names and labels with specific mutation rules to create custom wordlists. Hashcat uses a specific syntax to define characters, words, and their transformations. The complete syntax is documented in the official Hashcat rule-based attack documentation, but the examples below are sufficient to understand how Hashcat mutates input words.

| Function | Description |
|----------|-------------|
| `:` | Do nothing |
| `l` | Lowercase all letters |
| `u` | Uppercase all letters |
| `c` | Capitalize the first letter and lowercase others |
| `sXY` | Replace all instances of X with Y |
| `$!` | Add the exclamation character at the end |

Each rule is written on a new line and determines how a given word should be transformed. If we write the functions shown above into a file, it may look like this:

```bash
alexsdb@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

We can use the following command to apply the rules in `custom.rule` to each word in `password.list` and store the mutated results in `mut_password.list`:

```bash
alexsdb@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

In this case, the single input word will produce fifteen mutated variants.

## Generating wordlists using CeWL

We can use a tool called CeWL to scan potential words from a company's website and save them in a separate list. We can then combine this list with the desired rules to create a customized password list—one that has a higher probability of containing the correct password for an employee. We specify some parameters, like the depth to spider (`-d`), the minimum length of the word (`-m`), the storage of the found words in lowercase (`--lowercase`), as well as the file where we want to store the results (`-w`).

```bash
alexsdb@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
alexsdb@htb[/htb]$ wc -l inlane.wordlist

326
```