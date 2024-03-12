```
gobuster dir -u http://192.168.247.245/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt --threads 50
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.247.245/
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 199]
/.htaccess            (Status: 403) [Size: 199]
/css                  (Status: 301) [Size: 235] [--> http://192.168.247.245/css/]
/fonts                (Status: 301) [Size: 237] [--> http://192.168.247.245/fonts/]
/img                  (Status: 301) [Size: 235] [--> http://192.168.247.245/img/]
/js                   (Status: 301) [Size: 234] [--> http://192.168.247.245/js/]
Progress: 20476 / 20477 (100.00%)
===============================================================
Finished
===============================================================
```
### Results: Nothing relevant found.

