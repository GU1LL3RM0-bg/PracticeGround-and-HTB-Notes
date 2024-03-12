```
Nmap scan report for 192.168.247.248
Host is up, received user-set (0.040s latency).
Scanned at 2023-09-26 22:09:47 EDT for 282s
Not shown: 65520 closed tcp ports (reset)
PORT      STATE SERVICE       REASON          VERSION
80/tcp    open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
| http-robots.txt: 16 disallowed entries 
| /*/ctl/ /admin/ /App_Browsers/ /App_Code/ /App_Data/ 
| /App_GlobalResources/ /bin/ /Components/ /Config/ /contest/ /controls/ 
| /Documentation/ /HttpModules/ /Install/ /Providers/ 
|_/Activity-Feed/userId/
|_http-title: Home
|_http-favicon: Unknown favicon MD5: 2DE6897008EB657D2EC770FE5B909439
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
| ssl-cert: Subject: commonName=EXTERNAL
| Issuer: commonName=EXTERNAL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-26T01:43:32
| Not valid after:  2024-03-27T01:43:32
| MD5:   3afa:865f:c49e:6a86:6d94:db3a:7518:2c9e
| SHA-1: 1318:9995:c133:9c4d:d0e1:ff8e:4ebf:6b94:297f:32ac
| -----BEGIN CERTIFICATE-----
| MIIC1DCCAbygAwIBAgIQKO0ND5Ert6hDLBjHm+3myTANBgkqhkiG9w0BAQsFADAT
| MREwDwYDVQQDEwhFWFRFUk5BTDAeFw0yMzA5MjYwMTQzMzJaFw0yNDAzMjcwMTQz
| MzJaMBMxETAPBgNVBAMTCEVYVEVSTkFMMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAvMuftgVY6FP2agpgLdu0OMqMClhOGkwukAEYkixFRRReksRkyfh5
| uQYLusfgpQOcAAbpUR9uueMtKwnzgjrOX53T8fgG3q6Z985c/fhKkOO1CTbVx/Nr
| 3rjR+XQcCmqMNMQ4Q1VnT8AscKD8n9e/EDQc5rMu6ErHVmXJZ1VshIdqrW5++9lC
| 9hB9RR4QBETWecZovk3sKeQAdayCpMdTPQBykfThRmxerN4iAqvMVeK2bellZONf
| nYtgjk52soj3LXEyCVHNrkWqRdyLjcVL8TxlMtdfjSNoOwIOXQTW43O1ScfJkzRM
| VS6M1trrm33BYnC7B69iUnZrcQUfnBm7dQIDAQABoyQwIjATBgNVHSUEDDAKBggr
| BgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBABErkEtJY3RK
| Hidc2aFXrJZFRbmPEejejnkd7801yPnPkKO6V6CsN2z2sKFYsc/+RiTGwjXnE0vQ
| j1IJOH3Q/YXOC8cePlN2eXykpUPKIYXClGccDTXFGhmNfYN7cFsG6FGJbFKmgeEl
| sd3smOlvI65hzUd8fxmLw3daRR0DfrIhRR0L1NJPEkqUWaWdmovUoKJIfRyC2oHb
| EDA4/9m9hm+Eq5IvbvxZ1w4HX1chcKD7KrcOXJ/2Bk0TBBnaJkjJuXVZLdf31qme
| m1P9YnB2PIVNBsgABSbtBGKTmgCGWewU1W/x+XXow76Onu1FdRsobpUTSNBzQxE0
| 5vM7C6Wk8y4=
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: EXTERNAL
|   NetBIOS_Domain_Name: EXTERNAL
|   NetBIOS_Computer_Name: EXTERNAL
|   DNS_Domain_Name: EXTERNAL
|   DNS_Computer_Name: EXTERNAL
|   Product_Version: 10.0.20348
|_  System_Time: 2023-09-27T02:16:34+00:00
|_ssl-date: 2023-09-27T02:16:56+00:00; +2m28s from scanner time.
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49965/tcp open  ms-sql-s      syn-ack ttl 125 Microsoft SQL Server 2019 15.00.2000.00; RTM
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-03-01T06:42:58
| Not valid after:  2053-03-01T06:42:58
| MD5:   022f:e745:145a:cada:c06e:cb13:239b:465d
| SHA-1: a905:ee65:7daa:11d2:a294:82d2:b3c9:3b94:6d72:9373
| -----BEGIN CERTIFICATE-----
| MIIDADCCAeigAwIBAgIQOtQrLNfn+IhF0ZXlptCaojANBgkqhkiG9w0BAQsFADA7
| MTkwNwYDVQQDHjAAUwBTAEwAXwBTAGUAbABmAF8AUwBpAGcAbgBlAGQAXwBGAGEA
| bABsAGIAYQBjAGswIBcNMjMwMzAxMDY0MjU4WhgPMjA1MzAzMDEwNjQyNThaMDsx
| OTA3BgNVBAMeMABTAFMATABfAFMAZQBsAGYAXwBTAGkAZwBuAGUAZABfAEYAYQBs
| AGwAYgBhAGMAazCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAOGZFIF+
| tnPKxnC2Ac0Gtn/pM812NYNpaqxNImrILilpHDIVxklXE7EiO3PLfsB9mtAKeXKh
| 2NpPN0CN4p8Oe1LyozKJuHKpf9B4xgHHrU7WM2h8XU4zGmPvQ4j3ULfh+3jkr1Xm
| hboAUze5IMrUomdtU2224c+aI2vSVJOLKZ2n6iaBbpFVx26wcJp/D5ATKwoHu+fq
| g0UScRl8ElU0qx17zs2HtmQIXSV83wg7IatAturL4cmULjKrtPTzIsImOjtXzKdP
| +Mj7uYP8Akfr9xNazgcBYvivLEn1v60k80vOL49gWa/gmk0/P8A2tHvkWsGRbq0R
| 3+GNAjhdlLUBHt0CAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAVu1e7C+HcvZiN1OV
| eNKcgwU0VLEh4nkKwLHJ8HfdCEZ79YscSVuk042QN0FFVUuNZR9SwiYb9wY+pjvj
| 2NpDrK7UcgYTXZY4nXZLC+pTurcBC24nD3ZE38RGJnXkt9lW1WSbCBX2/QaQHeCT
| dEUoWGy5DAo0f0AwUHK2Q+5diuqjwD1gAgJEy3GsjQOUym99Qc+iP7a4c8qbvLdS
| SY+zgQFfrqmKsZ/LKI7ypPa1uorDneCSH7uhr67mqWgGMfDOn4D9g/U9q3TM7lLN
| tb/yIgCrlIaoAmFep+qbMquxa7kh4M6NnkINsuD/QESDEb0z1lqhB6ZOhDWCq/to
| dtwIrQ==
|_-----END CERTIFICATE-----
| ms-sql-ntlm-info: 
|   192.168.247.248:49965: 
|     Target_Name: EXTERNAL
|     NetBIOS_Domain_Name: EXTERNAL
|     NetBIOS_Computer_Name: EXTERNAL
|     DNS_Domain_Name: EXTERNAL
|     DNS_Computer_Name: EXTERNAL
|_    Product_Version: 10.0.20348
| ms-sql-info: 
|   192.168.247.248:49965: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 49965
|_ssl-date: 2023-09-27T02:16:56+00:00; +2m28s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-09-27T02:16:42
|_  start_date: N/A
|_clock-skew: mean: 2m28s, deviation: 0s, median: 2m27s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 48654/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 34358/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 60141/udp): CLEAN (Timeout)
|   Check 4 (port 65256/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

```