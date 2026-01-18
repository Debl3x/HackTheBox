# Introduction to John The Ripper

John the Ripper (aka. JtR aka. john) is a well-known penetration testing tool used for cracking passwords through various attacks including brute-force and dictionary.

The **"jumbo"** variant is recommended for our uses, as it has performance optimizations, additional features such as multilingual word lists, and support for 64-bit architectures. This version is able to crack passwords with greater accuracy and speed.

## Single Crack Mode

Single crack mode is a rule-based cracking technique that is most useful when targeting Linux credentials. It generates password candidates based on the victim's username, home directory name, and GECOS values (full name, room number, phone number, etc.). These strings are run against a large set of rules that apply common string modifications seen in passwords (e.g. a user whose real name is Bob Smith might use `Smith1` as their password).

### Example

If we got informations about a passwd file:

```bash
r0lf:$6$ues25dIanlctrWxg$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash
```

Using this command:

```bash
john --single passwd
```

## Wordlist Mode

Wordlist mode is used to crack passwords with a dictionary attack, meaning it attempts all passwords in a supplied wordlist against the password hash.

The basic syntax for the command is as follows:

```bash
john --wordlist=<wordlist_file> <hash_file>
```

The wordlist file (or files) used for cracking password hashes must be in plain text format, with one word per line. Multiple wordlists can be specified by separating them with a comma.

Rules, either custom or built-in, can be specified by using the `--rules` argument. These can be applied to generate candidate passwords using transformations such as appending numbers, capitalizing letters, and adding special characters.

## Incremental Mode

Incremental mode is a powerful, brute-force-style password cracking mode that generates candidate passwords based on a statistical model (Markov chains). It is designed to test all character combinations defined by a specific character set, prioritizing more likely passwords based on training data.

This mode is the most exhaustive, but also the most time-consuming. It generates password guesses dynamically and does not rely on a predefined wordlist, in contrast to wordlist mode.

Unlike purely random brute-force attacks, Incremental mode uses a statistical model to make educated guesses, resulting in a significantly more efficient approach than naïve brute-force attacks.

The basic syntax is:

```bash
john --incremental <hash_file>
```

## Identifying Unknown Hash Formats

Sometimes, password hashes may appear in an unknown format, and even John the Ripper (JtR) may not be able to identify them with complete certainty.

For example, consider the following hash:

```bash
193069ceb0461e1d40d216e32c79c704
```

One way to get an idea is to consult JtR's sample hash documentation, or this list by PentestMonkey. Both sources list multiple example hashes as well as the corresponding JtR format.

Another option is to use a tool like **hashID**, which checks supplied hashes against a built-in list to suggest potential formats. By adding the `-j` flag, hashID will, in addition to the hash format, list the corresponding JtR format:

```bash
alexsdb@htb[/htb]$ hashid -j 193069ceb0461e1d40d216e32c79c704
```

Output:

```bash
Analyzing '193069ceb0461e1d40d216e32c79c704'
[+] MD2 [JtR Format: md2]
[+] MD5 [JtR Format: raw-md5]
[+] MD4 [JtR Format: raw-md4]
[+] Double MD5
[+] LM [JtR Format: lm]
[+] RIPEMD-128 [JtR Format: ripemd-128]
[+] Haval-128 [JtR Format: haval-128-4]
[+] Tiger-128
[+] Skein-256(128)
[+] Skein-512(128)
[+] Lotus Notes/Domino 5 [JtR Format: lotus5]
[+] Skype
[+] Snefru-128 [JtR Format: snefru-128]
[+] NTLM [JtR Format: nt]
[+] Domain Cached Credentials [JtR Format: mscach]
[+] Domain Cached Credentials 2 [JtR Format: mscach2]
[+] DNSSEC(NSEC3)
[+] RAdmin v2.x [JtR Format: radmin]
```

Unfortunately, in our example it is still quite unclear what format the hash is in. This will sometimes be the case, and is simply one of the "problems" you will encounter as a pentester.

Many times, the context of where the hash came from will be enough to make an educated case on the format. In this specific example, the hash format is **RIPEMD-128**.

JtR supports hundreds of hash formats, some of which are listed in the table below. The `--format` argument can be supplied to instruct JtR which format target hashes have.

## Cracking Files

It is also possible to crack password-protected or encrypted files with JtR. Multiple **2john** tools come with JtR that can be used to process files and produce hashes compatible with JtR.

The generalized syntax for these tools is:

```bash
<tool> <file_to_crack> > file.hash
```
