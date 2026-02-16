# Recursive Fuzzing

So far, we've focused on fuzzing directories directly under the web root and files within a single directory. But what if our target has a complex structure with multiple nested directories? Manually fuzzing each level would be tedious and time-consuming. This is where recursive fuzzing comes in handy.

## How Recursive Fuzzing Works

Recursive fuzzing is an automated way to delve into the depths of a web application's directory structure through a 3-step process:

### 1. Initial Fuzzing
- The fuzzing process begins with the top-level directory, typically the web root (`/`)
- The fuzzer sends requests based on the provided wordlist containing potential directory and file names
- Server responses are analyzed for successful results (e.g., HTTP 200 OK) indicating existing directories

### 2. Directory Discovery and Expansion
- When a valid directory is found, the fuzzer creates a new branch by appending the directory name to the base URL
- Example: if `admin` is found, a new branch like `http://localhost/admin/` is created
- This new branch becomes the starting point for fresh fuzzing (e.g., `http://localhost/admin/FUZZ`)

### 3. Iterative Depth
- The process repeats for each discovered directory, creating further branches
- Continues until a specified depth limit is reached or no more valid directories are found
- Works like a tree structure where the web root is the trunk and each directory is a branch

## Why Use Recursive Fuzzing?

- **Efficiency**: Automates discovery of nested directories, saving significant time
- **Thoroughness**: Systematically explores every branch, reducing risk of missing assets
- **Reduced Manual Effort**: The tool handles the entire process automatically
- **Scalability**: Invaluable for large-scale web applications

## Recursive Fuzzing with ffuf

Start your target system and use the wordlist: `/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt`

### Basic Command
```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://IP:PORT/FUZZ -e .html -recursion
```

**Key flags:**
- `-recursion`: Enables recursive fuzzing
- `-ic`: Ignores wordlist comments (lines starting with `#`)
- `-v`: Verbose output

### Example Output
The fuzzer discovers `level1/` → identifies `level2/` and `level3/` → finds `index.html` files at each level. The `index.html` in `level3/` contains the flag: `HTB{r3curs1v3_fuzz1ng_w1ns}`

## Be Responsible

Recursive fuzzing can be resource-intensive. Use these options to mitigate risks:

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500
```

**Control options:**
- `-recursion-depth`: Set maximum depth (e.g., `-recursion-depth 2` = 2 levels)
- `-rate`: Control request rate per second
- `-timeout`: Set timeout for individual requests
