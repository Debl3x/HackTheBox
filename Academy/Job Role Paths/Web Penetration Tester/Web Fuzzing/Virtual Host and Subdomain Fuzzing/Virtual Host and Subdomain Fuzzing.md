# Virtual Host and Subdomain Fuzzing

## Overview

Virtual hosting (vhosting) and subdomains are essential for organizing web content:

- **Virtual Hosts**: Multiple websites served from a single server via the Host HTTP header
- **Subdomains**: Extensions of a primary domain (e.g., `blog.example.com`) resolved through DNS

| Aspect | Virtual Hosts | Subdomains |
|--------|---------------|-----------|
| Identification | Host header in HTTP requests | DNS records |
| Purpose | Host multiple websites on one server | Organize sections/services |
| Security Risk | Exposed internal apps or data | DNS record mismanagement |

## Gobuster

A command-line tool for discovering hidden directories, files, subdomains, and virtual hosts.

**Capabilities:**
- Directory/file enumeration
- Subdomain enumeration
- Virtual host discovery

## Virtual Host Fuzzing

### Setup

```bash
echo "IP inlanefreight.htb" | sudo tee -a /etc/hosts
```

### Command

```bash
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/Web-Content/common.txt --append-domain
```

**Flags:**
- `vhost`: Enable vhost fuzzing mode
- `-u`: Target URL
- `-w`: Wordlist path
- `--append-domain`: Append base domain to wordlist entries

### Results

Status `200` indicates valid, accessible vhosts (e.g., `admin.inlanefreight.htb`).

## Subdomain Fuzzing

### Command

```bash
gobuster dns -d inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Flags:**
- `dns`: Enable DNS fuzzing mode
- `-d`: Target domain
- `-w`: Wordlist of subdomain names

### Results

Each "Found:" entry indicates a valid, DNS-resolvable subdomain.

> **Note:** In latest Gobuster versions, use `--domain` instead of `-d` to specify the target domain.
