
## Found /dashboard directory with default config page for xampp.

```
gobuster dir -u http://192.168.247.247/ -w /usr/share/wordlists/dirb/big.txt --threads 100                       Tue 26 Sep 2023 10:35:00 PM EDT
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.247.247/
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 305]
/.htpasswd            (Status: 403) [Size: 305]
/assets               (Status: 301) [Size: 344] [--> http://192.168.247.247/assets/]
/aux                  (Status: 403) [Size: 305]
/cgi-bin/             (Status: 403) [Size: 305]
/com4                 (Status: 403) [Size: 305]
/com1                 (Status: 403) [Size: 305]
/com3                 (Status: 403) [Size: 305]
/com2                 (Status: 403) [Size: 305]
/con                  (Status: 403) [Size: 305]
/css                  (Status: 301) [Size: 341] [--> http://192.168.247.247/css/]
/dashboard            (Status: 301) [Size: 347] [--> http://192.168.247.247/dashboard/]
/favicon.ico          (Status: 200) [Size: 30894]
/img                  (Status: 301) [Size: 341] [--> http://192.168.247.247/img/]
/js                   (Status: 301) [Size: 340] [--> http://192.168.247.247/js/]
/licenses             (Status: 403) [Size: 424]
/lpt1                 (Status: 403) [Size: 305]
/lpt2                 (Status: 403) [Size: 305]
/examples             (Status: 503) [Size: 405]
/nul                  (Status: 403) [Size: 305]
/pdfs                 (Status: 301) [Size: 342] [--> http://192.168.247.247/pdfs/]
/phpmyadmin           (Status: 403) [Size: 305]
/prn                  (Status: 403) [Size: 305]
/secci�               (Status: 403) [Size: 305]
/server-info          (Status: 403) [Size: 424]
/server-status        (Status: 403) [Size: 424]
/webalizer            (Status: 403) [Size: 305]
/xampp                (Status: 301) [Size: 343] [--> http://192.168.247.247/xampp/]
Progress: 20469 / 20470 (100.00%)
===============================================================
Finished
===============================================================
```

