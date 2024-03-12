```
Nmap scan report for 192.168.247.191
Host is up, received user-set (0.039s latency).
Scanned at 2023-09-26 22:05:36 EDT for 251s
Not shown: 65520 closed tcp ports (reset)
PORT      STATE SERVICE       REASON          VERSION
80/tcp    open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=192.168.247.191
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 401 - Unauthorized: Access is denied due to invalid credentials.
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
|_ssl-date: 2023-09-27T02:12:13+00:00; +2m29s from scanner time.
| ssl-cert: Subject: commonName=login.relia.com
| Issuer: commonName=login.relia.com
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-26T01:43:57
| Not valid after:  2024-03-27T01:43:57
| MD5:   e1c6:f7a7:32d9:b962:02d9:0dd6:8504:1b4a
| SHA-1: 13b9:fdab:bd60:a3a4:af09:afac:7c04:2f24:51d3:ef91
| -----BEGIN CERTIFICATE-----
| MIIC4jCCAcqgAwIBAgIQbFjXnvEGJKlPWXz/6k7IRDANBgkqhkiG9w0BAQsFADAa
| MRgwFgYDVQQDEw9sb2dpbi5yZWxpYS5jb20wHhcNMjMwOTI2MDE0MzU3WhcNMjQw
| MzI3MDE0MzU3WjAaMRgwFgYDVQQDEw9sb2dpbi5yZWxpYS5jb20wggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQCvuEcgrZ+NHXtMDDWOtj/747bwhZMn1Wl/
| Vyz8670XqG12nz1WJqKrUUZ55c/udx1LiwfLzrOqHqVq1L0wQcmslXzALcPbV2ZU
| ugH1gt51GYEzH6oNSafzZ2Kh5emJEh6jsVjGpRbNXxwtu+5OTCFSJHDJnrZQKFyM
| h+WMQnCy4U3db5OoocSnrUER5J4wBen35fxM/CYwwSd/bLyhCyD7xaaZE8JiCO4D
| JYayjxQsRl1ABV4YtbcdTfI7HyKcZWkkPk+fGD+Oay1MR13Du5Y6LI9m86PRoR2W
| nr7Pv7ycnh8XMZiUIy/cgB3y1gCzE4QZBO2zII+6sQZrlr6HgQ91AgMBAAGjJDAi
| MBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsF
| AAOCAQEAi/mQyqfzFTDYVdLWCCplyRuEamOdF8AhkLcparIvkTcOvMnPmpyd9V6s
| XzJz58RvvWoM/ptKx3fNzCfS0u9Sn6hgBmaGQ2z+d8G4SjG52nMrc8hbhllGIuvH
| ReQs5e5ma35Pffphtx/WvRFT1+CfTV3NWZ4/KjvAfJSjPpz8KiGesV/+SbQhmiqo
| VV8ehwlEqXO06/P8hzCa260d2NmTF2N3G9Wt8OUvoXwILxuitNbtI4kfr/cf0zfY
| 3ovGVnNqmd8R+Tp1bfrtMS6tsx77s8qogWt12X9nkUQMl7tWQ3C/SChP60nz8Jxd
| bRFKHcLEuZua/5pjWKRAZqHANw6j9w==
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: RELIA
|   NetBIOS_Domain_Name: RELIA
|   NetBIOS_Computer_Name: LOGIN
|   DNS_Domain_Name: relia.com
|   DNS_Computer_Name: login.relia.com
|   DNS_Tree_Name: relia.com
|   Product_Version: 10.0.20348
|_  System_Time: 2023-09-27T02:11:58+00:00
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
49671/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 28052/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 14275/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 63375/udp): CLEAN (Failed to receive data)
|   Check 4 (port 31562/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 2m28s, deviation: 0s, median: 2m28s
| smb2-time: 
|   date: 2023-09-27T02:12:03
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
```

