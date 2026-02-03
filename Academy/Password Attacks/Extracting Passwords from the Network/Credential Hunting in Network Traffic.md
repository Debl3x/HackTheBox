# Credential Hunting in Network Traffic

Although most modern applications use TLS to encrypt sensitive data in transit, not all environments are properly secured. Legacy systems, misconfigured services, or development environments may still rely on plaintext protocols such as HTTP, FTP, or SNMP.

These situations provide attackers with valuable opportunities to capture credentials directly from network traffic. This section covers practical techniques for identifying exposed credentials using **Wireshark**, along with a brief overview of **Pcredz**, a tool designed to automatically extract credentials from packet captures.

---

## Plaintext vs Encrypted Protocols

The table below lists common plaintext protocols and their encrypted counterparts. While secure versions are now the norm, plaintext protocols are still encountered in poorly secured or legacy environments.

| Unencrypted Protocol | Encrypted Counterpart  | Description                                |
| -------------------- | ---------------------- | ------------------------------------------ |
| HTTP                 | HTTPS                  | Transfers web pages and web resources      |
| FTP                  | FTPS / SFTP            | Transfers files between client and server  |
| SNMP                 | SNMPv3 (encrypted)     | Manages and monitors network devices       |
| POP3                 | POP3S                  | Retrieves emails from a mail server        |
| IMAP                 | IMAPS                  | Manages emails directly on the mail server |
| SMTP                 | SMTPS                  | Sends email between clients and servers    |
| LDAP                 | LDAPS                  | Queries and manages directory services     |
| RDP                  | RDP with TLS           | Remote desktop access                      |
| DNS                  | DNS over HTTPS (DoH)   | Resolves domain names                      |
| SMB                  | SMB over TLS (SMB 3.0) | Network file and resource sharing          |
| VNC                  | VNC with TLS/SSL       | Remote graphical desktop access            |

---

## Wireshark

Wireshark is a powerful packet analyzer commonly pre-installed on penetration testing Linux distributions. It allows analysts to inspect both live and captured network traffic using an advanced filtering engine.

### Common Wireshark Display Filters

| Filter                                            | Description                             |
| ------------------------------------------------- | --------------------------------------- |
| `ip.addr == 56.48.210.13`                         | Filters packets involving a specific IP |
| `tcp.port == 80`                                  | Filters traffic on port 80 (HTTP)       |
| `http`                                            | Displays HTTP traffic                   |
| `dns`                                             | Displays DNS queries and responses      |
| `icmp`                                            | Displays ICMP traffic (e.g., ping)      |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0`        | Shows TCP SYN packets                   |
| `http.request.method == "POST"`                   | Filters HTTP POST requests              |
| `tcp.stream eq 53`                                | Displays a specific TCP conversation    |
| `eth.addr == 00:11:22:33:44:55`                   | Filters by MAC address                  |
| `ip.src == 192.168.24.3 && ip.dst == 56.48.210.3` | Traffic between two specific hosts      |

These filters help isolate traffic that may contain sensitive information, particularly when dealing with unencrypted protocols.

---

## Searching for Credentials in Wireshark

Wireshark allows searching for specific strings within packets.

### Methods

* Use a display filter:

  ```text
  http contains "passw"
  ```
* Or navigate to:
  **Edit → Find Packet**, then search for keywords such as:

  * `password`
  * `username`
  * `login`
  * `auth`

If HTTP POST requests are sent over plaintext HTTP, credentials often appear directly in the request body.

---

## Pcredz

Pcredz is a tool designed to automatically extract credentials from live traffic or packet capture files.

### Supported Credential Types

Pcredz can extract, among others:

* Credit card numbers
* POP, IMAP, SMTP credentials
* FTP credentials
* SNMP community strings
* HTTP Basic and NTLM authentication credentials
* HTTP form credentials
* NTLMv1/v2 hashes from:

  * SMB
  * LDAP
  * MSSQL
  * RPC
  * HTTP
* Kerberos (AS-REQ Pre-Auth etype 23) hashes

---

## Running Pcredz

Pcredz can be run either by cloning the repository and installing dependencies or by using the Docker container provided in the project documentation.

### Analyzing a Packet Capture File

```bash
./Pcredz -f demo.pcapng -t -v
```

### Example Output

```text
Found SNMPv2 Community string: s3cr...
FTP User: user
FTP Pass: password
```

Pcredz processes packet captures quickly and provides immediate visibility into exposed credentials.

---

## Conclusion

Credential hunting in network traffic relies on:

* Identifying plaintext protocols
* Efficient filtering and inspection with Wireshark
* Automated extraction using tools like Pcredz

Even in modern environments, misconfigurations and legacy systems can expose sensitive data. Network traffic analysis remains a powerful technique for discovering credentials and gaining further access within a target environment.
