```
nikto -h http://192.168.247.248/                                                                                         Wed 27 Sep 2023 12:02:02 AM EDT
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          192.168.247.248
+ Target Hostname:    192.168.247.248
+ Target Port:        80
+ Start Time:         2023-09-27 00:02:09 (GMT-4)
---------------------------------------------------------------------------
+ Server: No banner retrieved
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /7uAKg7J2.aspx: Uncommon header 'x-result-reason' found, with contents: Not Redirected.
+ /7uAKg7J2.aspx: Uncommon header 'x-urlrewriter-404' found, with contents: 404 Rewritten to DNN Tab : 404 Error Page(Tabid:24) : Reason Requested_404.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /admin/: Uncommon header 'x-redirect-reason' found, with contents: Unfriendly Url 2 Requested.
+ /robots.txt: contains 16 entries which should be manually viewed. See: https://developer.mozilla.org/en-US/docs/Glossary/Robots.txt
+ OPTIONS: Allowed HTTP Methods: OPTIONS, TRACE, GET, HEAD, POST .
+ OPTIONS: Public HTTP Methods: OPTIONS, TRACE, GET, HEAD, POST .
+ /login/: This might be interesting.

+ /Portals/_default/Cache/ReadMe.txt: DotNetNuke default page found. Look for an admin interface on /tabid/19/, /tabid/36/ or enumerate numbers to identify logins/content.
+ 8119 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2023-09-27 00:07:54 (GMT-4) (345 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested

```