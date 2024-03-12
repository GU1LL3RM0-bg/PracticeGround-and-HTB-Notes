HTTP:

```
nikto -h http://192.168.247.245/                                                                                 Tue 26 Sep 2023 10:54:24 PM EDT
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          192.168.247.245
+ Target Hostname:    192.168.247.245
+ Target Port:        80
+ Start Time:         2023-09-26 23:26:01 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.49 (Unix) OpenSSL/1.1.1f mod_wsgi/4.9.4 Python/3.8
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Python/3.8 appears to be outdated (current is at least 3.9.6).
+ OpenSSL/1.1.1f appears to be outdated (current is at least 3.0.7). OpenSSL 1.1.1s is current for the 1.x branch and will be supported until Nov 11 2023.
+ Apache/2.4.49 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ OPTIONS: Allowed HTTP Methods: GET, POST, OPTIONS, HEAD, TRACE .
+ /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
+ /css/: Directory indexing found.
+ /css/: This might be interesting.
+ /img/: Directory indexing found.
+ /img/: This might be interesting.
+ 8101 requests: 0 error(s) and 11 item(s) reported on remote host
+ End Time:           2023-09-26 23:30:52 (GMT-4) (291 seconds)

```

## Results: Nothing relevant found.

