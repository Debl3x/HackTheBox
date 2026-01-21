# Cracking Protected Files

The use of file encryption is often neglected in both private and professional contexts. Even today, emails containing job applications, account statements, or contracts are frequently sent without encryption—sometimes in violation of legal regulations. For example, within the European Union, the General Data Protection Regulation (GDPR) requires that personal data be encrypted both in transit and at rest. Nevertheless, it remains standard practice to discuss confidential topics or transmit sensitive data via email, which may be intercepted by attackers positioned to exploit these communication channels.

## Hunting for Encrypted Files

Many different extensions correspond to encrypted files—a useful reference list can be found on FileInfo. As an example, consider this command we might use to locate commonly encrypted files on a Linux system:

```bash
alexsdb@htb[/htb]$ for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

```
File extension:  .xls

File extension:  .xls*

File extension:  .xltx

File extension:  .od*
/home/cry0l1t3/Docs/document-temp.odt
/home/cry0l1t3/Docs/product-improvements.odp
/home/cry0l1t3/Docs/mgmt-spreadsheet.ods
...SNIP...
```

## Hunting for SSH Keys

Certain files, such as SSH keys, do not have standard file extensions. In cases like these, it may be possible to identify files by standard content such as header and footer values. For example, SSH private keys always begin with `-----BEGIN [...SNIP...] PRIVATE KEY-----`. We can use tools like `grep` to recursively search the file system for them during post-exploitation.

```bash
alexsdb@htb[/htb]$ grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null
```

```
/home/jsmith/.ssh/id_ed25519:1:-----BEGIN OPENSSH PRIVATE KEY-----
/home/jsmith/.ssh/SSH.private:1:-----BEGIN RSA PRIVATE KEY-----
/home/jsmith/Documents/id_rsa:1:-----BEGIN OPENSSH PRIVATE KEY-----
<SNIP>
```

Some SSH keys are encrypted with a passphrase. With older PEM formats, it was possible to tell if an SSH key is encrypted based on the header, which contains the encryption method in use. Modern SSH keys, however, appear the same whether encrypted or not.

```bash
alexsdb@htb[/htb]$ cat /home/jsmith/.ssh/SSH.private
```

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2109D25CC91F8DBFCEB0F7589066B2CC

8Uboy0afrTahejVGmB7kgvxkqJLOczb1I0/hEzPU1leCqhCKBlxYldM2s65jhflD
4/OH4ENhU7qpJ62KlrnZhFX8UwYBmebNDvG12oE7i21hB/9UqZmmHktjD3+OYTsD
<SNIP>
```

One way to tell whether an SSH key is encrypted or not is to try reading the key with `ssh-keygen`.

```bash
alexsdb@htb[/htb]$ ssh-keygen -yf ~/.ssh/id_ed25519
```

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIpNefJd834VkD5iq+22Zh59Gzmmtzo6rAffCx2UtaS6
```

Attempting to read a password-protected SSH key will prompt the user for a passphrase:

```bash
alexsdb@htb[/htb]$ ssh-keygen -yf ~/.ssh/id_rsa

Enter passphrase for "/home/jsmith/.ssh/id_rsa"
```

```bash
alexsdb@htb[/htb]$ locate *2john*
```

```
/usr/bin/bitlocker2john
/usr/bin/dmg2john
/usr/bin/gpg2john
/usr/bin/hccap2john
/usr/bin/keepass2john
/usr/bin/putty2john
/usr/bin/racf2john
/usr/bin/rar2john
/usr/bin/uaf2john
/usr/bin/vncpcap2john
/usr/bin/wlanhcx2john
/usr/bin/wpapcap2john
/usr/bin/zip2john
/usr/share/john/1password2john.py
/usr/share/john/7z2john.pl
/usr/share/john/DPAPImk2john.py
/usr/share/john/adxcsouf2john.py
/usr/share/john/aem2john.py
/usr/share/john/aix2john.pl
/usr/share/john/aix2john.py
/usr/share/john/andotp2john.py
/usr/share/john/androidbackup2john.py
<SNIP>
```

## Cracking Password-Protected Documents

Over the course of our careers, we are likely to encounter a wide variety of documents that are password-protected to restrict access to authorized individuals. Today, most reports, documentation, and information sheets are commonly distributed as Microsoft Office documents or PDFs. John the Ripper (JtR) includes a Python script called `office2john.py`, which can be used to extract password hashes from all common Office document formats. These hashes can then be supplied to JtR or Hashcat for offline cracking. The cracking procedure remains consistent with other hash types.

```bash
alexsdb@htb[/htb]$ office2john.py Protected.docx > protected-docx.hash
alexsdb@htb[/htb]$ john --wordlist=rockyou.txt protected-docx.hash
alexsdb@htb[/htb]$ john protected-docx.hash --show
```

```
Protected.docx:1234

1 password hash cracked, 0 left
```
