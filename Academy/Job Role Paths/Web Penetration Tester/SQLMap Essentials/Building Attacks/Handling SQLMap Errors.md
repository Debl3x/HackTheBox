# Handling SQLMap Errors

We may face many problems when setting up SQLMap or using it with HTTP requests. This section discusses recommended mechanisms for finding the cause and properly fixing it.

## Display Errors

Use the `--parse-errors` flag to parse DBMS errors and display them during program execution:

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" --parse-errors
```

Example output:
```
[16:09:20] [WARNING] parsed DBMS error message: 'SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax...'
```

SQLMap automatically prints DBMS errors, providing clarity on issues so you can fix them properly.

## Store the Traffic

The `-t` option stores all traffic content to an output file:

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt
cat /tmp/traffic.txt
```

This file contains all sent and received HTTP requests, allowing manual investigation of where issues occur.

## Verbose Output

The `-v` option raises console output verbosity:

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch
```

The `-v 6` option prints all errors and full HTTP requests to the terminal in real-time, helping you follow SQLMap's operations.

## Using Proxy

Use the `--proxy` option to redirect traffic through a Man-in-the-Middle (MiTM) proxy (e.g., Burp Suite):

```bash
sqlmap -u "http://www.target.com/vuln.php?id=1" --proxy "http://127.0.0.1:8080"
```

This routes all SQLMap traffic through your proxy for manual investigation, request repetition, and full feature utilization.
