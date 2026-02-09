# Proxying Tools

An important aspect of using web proxies is enabling the interception of web requests made by command-line tools and thick client applications. This gives us transparency into the web requests made by these applications and allows us to utilize all of the different proxy features we have used with web applications.

To route all web requests made by a specific tool through our web proxy tools, we have to set them up as the tool's proxy (i.e. `http://127.0.0.1:8080`), similarly to what we did with our browsers. Each tool may have a different method for setting its proxy, so we may have to investigate how to do so for each one.

This section will cover a few examples of how to use web proxies to intercept web requests made by such tools. You may use either Burp or ZAP, as the setup process is the same.

> **Note:** Proxying tools usually slows them down, therefore, only proxy tools when you need to investigate their requests, and not for normal usage.

## Proxychains

One very useful tool in Linux is **proxychains**, which routes all traffic coming from any command-line tool to any proxy we specify. Proxychains adds a proxy to any command-line tool and is hence the simplest and easiest method to route web traffic of command-line tools through our web proxies.

To use proxychains, edit `/etc/proxychains.conf`, comment out the final line and add:

```
#socks4         127.0.0.1 9050
http 127.0.0.1 8080
```

Use the `-q` option for "quiet" mode, which suppresses connection output:

```bash
alexsdb@htb[/htb]$ proxychains -q curl http://SERVER_IP:PORT
```

The request will appear in your web proxy history.

## Metasploit

To proxy web traffic made by Metasploit modules, start `msfconsole` and use the `set PROXIES` flag:

```bash
msf6 > use auxiliary/scanner/http/robots_txt
msf6 auxiliary(scanner/http/robots_txt) > set PROXIES HTTP:127.0.0.1:8080
msf6 auxiliary(scanner/http/robots_txt) > set RHOST SERVER_IP
msf6 auxiliary(scanner/http/robots_txt) > set RPORT PORT
msf6 auxiliary(scanner/http/robots_txt) > run
```

All requests will be visible in your web proxy history.

## Summary

You can similarly use web proxies with other tools and applications, including scripts and thick clients. This allows you to examine exactly what these tools are sending and receiving, and potentially repeat and modify their requests during web application penetration testing.
