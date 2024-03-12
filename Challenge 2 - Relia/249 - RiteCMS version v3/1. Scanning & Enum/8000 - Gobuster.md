```
===============================================================
[+] Url:                     http://192.168.203.249:8000/
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 307]
/.htpasswd            (Status: 403) [Size: 307]
/CMS                  (Status: 301) [Size: 348] [--> http://192.168.203.249:8000/CMS/]
/aux                  (Status: 403) [Size: 307]
/cgi-bin/             (Status: 403) [Size: 307]
/cms                  (Status: 301) [Size: 348] [--> http://192.168.203.249:8000/cms/]
/com1                 (Status: 403) [Size: 307]
/com2                 (Status: 403) [Size: 307]
/com4                 (Status: 403) [Size: 307]
/com3                 (Status: 403) [Size: 307]
/con                  (Status: 403) [Size: 307]
/dashboard            (Status: 301) [Size: 354] [--> http://192.168.203.249:8000/dashboard/]
/favicon.ico          (Status: 200) [Size: 30894]
/img                  (Status: 301) [Size: 348] [--> http://192.168.203.249:8000/img/]
/examples             (Status: 503) [Size: 407]
/licenses             (Status: 403) [Size: 426]
/lpt1                 (Status: 403) [Size: 307]
/lpt2                 (Status: 403) [Size: 307]
/nul                  (Status: 403) [Size: 307]
/phpmyadmin           (Status: 403) [Size: 426]
/prn                  (Status: 403) [Size: 307]
/server-status        (Status: 403) [Size: 426]
/server-info          (Status: 403) [Size: 426]
/webalizer            (Status: 403) [Size: 426]
/xampp                (Status: 301) [Size: 350] [--> http://192.168.203.249:8000/xampp/]
Progress: 20476 / 20477 (100.00%)
===============================================================
Finished
===============================================================

```
Found potentially vulnerable CMS on port 8000/CMS./


