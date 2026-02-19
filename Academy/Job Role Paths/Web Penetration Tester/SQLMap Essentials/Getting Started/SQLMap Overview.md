# SQLMap Overview

SQLMap is a free and open-source penetration testing tool written in Python that automates the process of detecting and exploiting SQL injection (SQLi) flaws. SQLMap has been continuously developed since 2006 and is still maintained today.

## Basic Usage Example

```bash
python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'
```

## Key Features

SQLMap comes with a powerful detection engine and numerous features for fine-tuning:

- **Target connection** & **Injection detection**
- **Fingerprinting** & **Enumeration**
- **Optimization** & **Protection detection** (with tamper scripts)
- **Database content retrieval** & **File system access**
- **OS command execution**

## Installation

SQLMap is pre-installed on Pwnbox and most security-focused Linux distributions.

**Via package manager (Debian):**
```bash
sudo apt install sqlmap
```

**Manual installation:**
```bash
git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
python sqlmap.py
```

## Supported Databases

MySQL, Oracle, PostgreSQL, Microsoft SQL Server, SQLite, IBM DB2, Microsoft Access, Firebird, Sybase, SAP MaxDB, Informix, MariaDB, HSQLDB, CockroachDB, TiDB, MemSQL, H2, MonetDB, Apache Derby, Amazon Redshift, Vertica, Presto, Altibase, MimerSQL, CrateDB, Greenplum, Drizzle, Apache Ignite, Cubrid, InterSystems Cache, IRIS, eXtremeDB, FrontBase

## SQL Injection Types Supported

| Type | Technique | Description |
|------|-----------|-------------|
| **Boolean-based blind** | B | Differentiates TRUE/FALSE results; most common type |
| **Error-based** | E | Uses DBMS error messages; fastest except UNION-based |
| **UNION query-based** | U | Fastest type; extends original query with injected results |
| **Stacked queries** | S | Injects additional SQL statements; requires platform support |
| **Time-based blind** | T | Uses response time differentiation; slower but reliable |
| **Inline queries** | Q | Embeds query within original query; uncommon |
| **Out-of-band** | - | Advanced type using DNS exfiltration |

Default technique setting: `--technique=BEUSTQ`
