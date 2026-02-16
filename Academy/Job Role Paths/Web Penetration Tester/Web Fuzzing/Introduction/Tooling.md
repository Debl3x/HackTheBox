# Tooling

In this module, we will utilize four powerful tools designed for web application reconnaissance and vulnerability assessment. To streamline our setup, we'll install them all upfront.

## Installing Go, Python, and PIPX

You will require Go and Python installed for these tools. Install them as follows if you don't have them installed already.

**pipx** is a command-line tool designed to simplify the installation and management of Python applications. It creates isolated virtual environments for each application, ensuring dependencies don't conflict.

### For Debian-based Systems (Ubuntu, etc.)

```bash
sudo apt update
sudo apt install -y golang
sudo apt install -y python3 python3-pip
sudo apt install pipx
pipx ensurepath
sudo pipx ensurepath --global
```

### Verify Installation

```bash
go version
python3 --version
```

---

## FFUF

**FFUF** (Fuzz Faster U Fool) is a fast web fuzzer written in Go for enumerating directories, files, and parameters.

```bash
go install github.com/ffuf/ffuf/v2@latest
```

| Use Case | Description |
|----------|-------------|
| Directory and File Enumeration | Quickly identify hidden directories and files on a web server |
| Parameter Discovery | Find and test parameters within web applications |
| Brute-Force Attack | Perform brute-force attacks to discover credentials |

---

## Gobuster

**Gobuster** is a popular web directory and file fuzzer known for speed and simplicity.

```bash
go install github.com/OJ/gobuster/v3@latest
```

| Use Case | Description |
|----------|-------------|
| Content Discovery | Find hidden web content (directories, files, virtual hosts) |
| DNS Subdomain Enumeration | Identify subdomains of a target domain |
| WordPress Content Detection | Find WordPress-related content using specific wordlists |

---

## FeroxBuster

**FeroxBuster** is a fast, recursive content discovery tool written in Rust for identifying hidden directories and files.

```bash
curl -sL https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh | sudo bash -s $HOME/.local/bin
```

| Use Case | Description |
|----------|-------------|
| Recursive Scanning | Discover nested directories and files |
| Unlinked Content Discovery | Identify non-linked web application content |
| High-Performance Scans | Conduct high-speed content discovery |

---

## wfuzz/wenum

**wenum** is an actively maintained fork of wfuzz, a versatile fuzzing tool for parameter testing and vulnerability discovery.

> **Note:** wfuzz may be pre-installed on PwnBox or Kali. Due to installation complications, wenum is recommended as a substitute with identical syntax.

```bash
pipx install git+https://github.com/WebFuzzForge/wenum
pipx runpip wenum install setuptools
```

| Use Case | Description |
|----------|-------------|
| Directory and File Enumeration | Quickly identify hidden directories and files |
| Parameter Discovery | Find and test parameters within web applications |
| Brute-Force Attack | Perform brute-force attacks to discover credentials |
