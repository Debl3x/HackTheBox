# Credential Hunting in Network Shares

Most corporate environments rely heavily on network shares to store and exchange files between employees and teams. While these shares are essential for collaboration, they often become a significant attack surface when sensitive data such as plaintext credentials, configuration files, or scripts are stored insecurely.

This section covers techniques and tools used to hunt for credentials across network shares from both **Windows** and **Linux** systems, focusing on automation while highlighting the need for careful manual analysis.

---

## Common Credential Patterns

Before using automated tools, it is important to understand common indicators of sensitive information. These concepts were covered earlier, but key reminders include:

* Search for keywords such as:

  * `passw`, `password`
  * `user`, `username`
  * `token`, `key`, `secret`
* Look for file extensions commonly associated with credentials:

  * `.ini`, `.cfg`, `.env`
  * `.xlsx`, `.csv`
  * `.ps1`, `.bat`
* Pay attention to filenames containing words like:

  * `config`, `cred`, `pass`, `user`, `initial`
* Domain-specific strings can be useful (e.g. `INLANEFREIGHT\`)
* Localize keywords based on the target environment (e.g. `Benutzer` instead of `User` in German environments)
* Be strategic when choosing shares to scan:

  * IT and administrative shares are usually higher value than marketing or media shares

Before scaling to automated tools, basic command-line searches can be effective, such as recursive file searches combined with string matching.

---

## Hunting from Windows

### Snaffler

Snaffler is a C# tool designed to run on a **domain-joined Windows system**. It automatically discovers accessible network shares and searches them for potentially sensitive files.

A basic scan can be performed as follows:

```cmd
Snaffler.exe -s
```

Snaffler will:

* Enumerate domain computers
* Discover accessible SMB shares
* Search files using predefined and configurable rules
* Highlight high-risk findings such as credentials in configuration files

Example findings may include:

* Credentials in unattended installation files (`unattend.xml`)
* Passwords embedded in scripts or configuration files
* Sensitive deployment artifacts

Because Snaffler generates a large amount of output, manual review is essential to distinguish real findings from false positives.

#### Useful Snaffler Options

* `-u`
  Retrieves users from Active Directory and searches for references to them in files
* `-i` / `-n`
  Include or exclude specific shares to focus the search

---

### PowerHuntShares

PowerHuntShares is a PowerShell-based tool that does **not require a domain-joined machine**. One of its main advantages is the generation of an **HTML report**, making result review significantly easier.

A basic scan can be executed as follows:

```powershell
Invoke-HuntSMBShares -Threads 100 -OutputDirectory C:\Users\Public
```

PowerHuntShares performs:

* Domain enumeration
* SMB share discovery
* Permission analysis
* Risk classification of shares
* Timeline generation
* HTML and CSV report creation

> Note: In large environments, this scan can take several hours to complete.

---

## Hunting from Linux

### MANSPIDER

MANSPIDER allows searching SMB shares remotely from Linux systems. It is recommended to use the **official Docker container** to avoid dependency issues.

A basic scan searching for files containing the string `passw`:

```bash
docker run --rm -v ./manspider:/root/.manspider \
  blacklanternsecurity/manspider 10.129.234.121 \
  -c 'passw' -u 'mendres' -p 'Inlanefreight2025!'
```

MANSPIDER features include:

* Remote SMB authentication
* File content searching
* Automatic downloading of matching files
* Threaded execution with file size limits

Downloaded files are stored locally for further analysis.

---

### NetExec

NetExec (formerly CrackMapExec) includes SMB spidering functionality using the `--spider` option.

A basic scan of the `IT` share for files containing `passw`:

```bash
nxc smb 10.129.234.121 -u mendres -p 'Inlanefreight2025!' \
  --spider IT --content --pattern "passw"
```

NetExec can:

* Authenticate to SMB services
* Enumerate shares
* Recursively search file contents
* Integrate share enumeration with other post-exploitation actions

---

## Manual Review and False Positives

All tools discussed in this section generate large volumes of data. Automation significantly speeds up discovery, but **manual validation remains critical**.

Common false positives include:

* Test credentials
* Documentation examples
* Disabled or obsolete configurations

Careful inspection of file context is required before drawing conclusions or attempting credential reuse.

---

## Conclusion

Credential hunting in network shares is a highly effective technique due to:

* Poor access control
* Excessive data retention
* Reuse of plaintext credentials

By combining:

* Strategic targeting
* Automated tools
* Manual analysis

attackers can often uncover credentials that lead to lateral movement or privilege escalation within corporate environments.
