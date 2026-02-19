# SQLMap Output Description

At the end of the previous section, the sqlmap output showed us a lot of info during its scan. This data is usually crucial to understand, as it guides us through the automated SQL injection process. This shows us exactly what kind of vulnerabilities SQLMap is exploiting, which helps us report what type of injection the web application has. This can also become handy if we wanted to manually exploit the web application once SQLMap determines the type of injection and vulnerable parameter.

## Log Messages Description

The following are some of the most common messages usually found during a scan of SQLMap:

### URL content is stable
**Message:** `target URL content is stable`

Indicates that responses are consistent between identical requests. This is important for automation since stable responses make it easier to spot differences from SQLi attempts. SQLMap has advanced mechanisms to filter potential "noise" from unstable targets.

### Parameter appears to be dynamic
**Message:** `GET parameter 'id' appears to be dynamic`

Desired behavior showing that changes to the parameter value result in response changes, indicating a potential database link. Static parameters that don't change suggest the value is not processed by the target.

### Parameter might be injectable
**Message:** `heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')`

DBMS errors (e.g., from invalid values like `?id=1",)..))'`) indicate potential SQLi. This is an indication for further testing, not definitive proof.

### Parameter might be vulnerable to XSS attacks
**Message:** `heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks`

SQLMap performs quick heuristic XSS checks alongside SQLi testing, useful for large-scale assessments.

### Back-end DBMS is '...'
**Message:** `it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n]`

When SQLMap identifies the DBMS, you can narrow testing to that specific database type.

### Level/risk values
**Message:** `for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n]`

Allows extending tests beyond default payloads for the detected DBMS.

### Reflective values found
**Message:** `reflective value(s) found and filtering out`

Warning that payload parts appear in responses. SQLMap filters this "junk" before comparing content.

### Parameter appears to be injectable
**Message:** `GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")`

Indicates successful detection of a specific SQLi type. The `--string="luther"` parameter shows SQLMap identified a constant value for distinguishing TRUE/FALSE responses, reducing false-positive risk.

### Time-based comparison statistical model
**Message:** `time-based comparison requires a larger statistical model, please wait........... (done)`

SQLMap collects response times to statistically distinguish deliberate delays from network latency.

### Extending UNION query injection technique tests
**Message:** `automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found`

UNION-query checks extend default requests (capped at 10) when other SQLi techniques are detected, improving success probability.

### Technique appears to be usable
**Message:** `ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test`

ORDER BY technique enables binary-search approach to identify the correct number of UNION columns needed.

### Parameter is vulnerable
**Message:** `GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N]`

Confirms successful exploitation of the parameter. Continue testing if seeking all vulnerabilities.

### SQLMap identified injection points
**Message:** `sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:`

Lists all confirmed exploitable injection points with type, title, and payloads.

### Data logged to text files
**Message:** `fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com'`

Storage location for logs, sessions, and output data. Future runs reference these session files to reduce target requests.
