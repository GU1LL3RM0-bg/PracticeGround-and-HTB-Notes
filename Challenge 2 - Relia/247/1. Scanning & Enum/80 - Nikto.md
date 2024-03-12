```
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          192.168.247.247
+ Target Hostname:    192.168.247.247
+ Target Port:        80
+ Start Time:         2023-09-26 22:42:56 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.54 (Win64) OpenSSL/1.1.1p PHP/8.1.10
+ /: Retrieved x-powered-by header: PHP/8.1.10.
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ OpenSSL/1.1.1p appears to be outdated (current is at least 3.0.7). OpenSSL 1.1.1s is current for the 1.x branch and will be supported until Nov 11 2023.
+ /: Web Server returns a valid response with junk HTTP methods which may cause false positives.
+ /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
+ /css/: Directory indexing found.
+ /css/: This might be interesting.
+ /img/: Directory indexing found.
+ /img/: This might be interesting.
+ /icons/: Directory indexing found.
+ /pdfs/: Directory indexing found.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
+ 8880 requests: 0 error(s) and 13 item(s) reported on remote host
+ End Time:           2023-09-26 22:49:22 (GMT-4) (386 seconds)

```