# Automating Recon

While manual reconnaissance can be effective, it can also be time-consuming and prone to human error. Automating web reconnaissance tasks can significantly enhance efficiency and accuracy, allowing you to gather information at scale and identify potential vulnerabilities more rapidly.

## Why Automate Reconnaissance?

Automation offers several key advantages for web reconnaissance:

- **Efficiency**: Automated tools perform repetitive tasks much faster than humans, freeing up valuable time for analysis and decision-making.
- **Scalability**: Scale reconnaissance efforts across multiple targets or domains, uncovering broader information scope.
- **Consistency**: Tools follow predefined rules and procedures, ensuring consistent, reproducible results while minimizing human error.
- **Comprehensive Coverage**: Perform DNS enumeration, subdomain discovery, web crawling, port scanning, and more.
- **Integration**: Easy integration with other tools and platforms, creating seamless workflows from reconnaissance to exploitation.

## Reconnaissance Frameworks

| Tool | Description |
|------|-------------|
| **FinalRecon** | Python-based tool with modules for SSL checking, Whois lookup, header analysis, and crawling |
| **Recon-ng** | Modular Python framework for DNS enumeration, subdomain discovery, and vulnerability exploitation |
| **theHarvester** | Gathers emails, subdomains, hosts, and employee names from public sources |
| **SpiderFoot** | Open-source intelligence automation tool with DNS lookups and web crawling |
| **OSINT Framework** | Collection of tools for intelligence gathering across multiple sources |

## FinalRecon

FinalRecon provides comprehensive reconnaissance capabilities:

- **Header Information**: Server details, technologies, and security misconfigurations
- **Whois Lookup**: Domain registration and contact details
- **SSL Certificate Analysis**: Validity, issuer information
- **Crawler**: Extracts links, resources, and uncovers hidden endpoints
- **DNS Enumeration**: Queries 40+ DNS record types
- **Subdomain Enumeration**: Leverages multiple data sources (crt.sh, VirusTotal API, Shodan API, etc.)
- **Directory Enumeration**: Supports custom wordlists and file extensions
- **Wayback Machine**: Historical website data analysis

### Installation

```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
cd FinalRecon
pip3 install -r requirements.txt
chmod +x ./finalrecon.py
./finalrecon.py --help
```

### Usage Options

| Option | Argument | Description |
|--------|----------|-------------|
| `-h, --help` | | Show help message |
| `--url` | URL | Target URL |
| `--headers` | | Retrieve header information |
| `--sslinfo` | | SSL certificate information |
| `--whois` | | Whois lookup |
| `--crawl` | | Crawl target website |
| `--dns` | | DNS enumeration |
| `--sub` | | Subdomain enumeration |
| `--dir` | | Directory search |
| `--wayback` | | Retrieve Wayback URLs |
| `--ps` | | Fast port scan |
| `--full` | | Full reconnaissance scan |

### Example Command

```bash
./finalrecon.py --headers --whois --url http://inlanefreight.com
```

This command retrieves header information and Whois data for the specified target URL.
