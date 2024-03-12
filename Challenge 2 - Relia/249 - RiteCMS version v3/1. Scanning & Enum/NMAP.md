```
Nmap scan report for 192.168.247.249
Host is up, received user-set (0.040s latency).
Scanned at 2023-09-26 22:09:47 EDT for 282s
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE       REASON          VERSION
80/tcp    open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: LEGACY
|   NetBIOS_Domain_Name: LEGACY
|   NetBIOS_Computer_Name: LEGACY
|   DNS_Domain_Name: LEGACY
|   DNS_Computer_Name: LEGACY
|   Product_Version: 10.0.20348
|_  System_Time: 2023-09-27T02:16:33+00:00
| ssl-cert: Subject: commonName=LEGACY
| Issuer: commonName=LEGACY
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-26T01:43:31
| Not valid after:  2024-03-27T01:43:31
| MD5:   340f:b391:53ab:3911:6ea5:bddd:075d:d7f5
| SHA-1: 2fab:ec0a:453a:73ef:8e75:9422:9fdf:94a6:04b0:ec62
| -----BEGIN CERTIFICATE-----
| MIIC0DCCAbigAwIBAgIQIb9tR2HfpbFODqtzu97NhTANBgkqhkiG9w0BAQsFADAR
| MQ8wDQYDVQQDEwZMRUdBQ1kwHhcNMjMwOTI2MDE0MzMxWhcNMjQwMzI3MDE0MzMx
| WjARMQ8wDQYDVQQDEwZMRUdBQ1kwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEK
| AoIBAQCcFszs/t52MF2WX88HfKCK5TIo8v9Fp9VssnxQ2MhwAgU+MrQ0IIPgSNzX
| gKrLuw4rY87oujCNZMr+EinDIaWXkzWdA3tLiID+3vkhgez437I/EjKvWyj6d8d6
| G1UVqslZ07KlUCmn7H29OQeC40JbMa6cQbl3ySYLM4Xm/bAXx/nrJ10h5yU81BQ/
| ejyg/KiP8DNmVAEWawRKI73c+BJZZ1VCCPID7KZdZ09zrGqJk1HnBfB7SObouh6q
| GLBqRqPTYfemM9pB2GMjzDbJbA3uObkVadyxB7dSHXUCfxNx/4kvaUHp7j+Tl5K+
| 6FPrH+h+VRkXT9wRGOzOUKu5m6EhAgMBAAGjJDAiMBMGA1UdJQQMMAoGCCsGAQUF
| BwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsFAAOCAQEAmZSAQjwvqiZYH/cz
| sHDKzKnWVS5u222QCtLXGtUlQmlOFDvRp24TljVEZ8jFopMoy1fNBsZJ2HWyi4qe
| 27q3FtvtRA6UyMwGRxMmmNsuhDJCNxBhde4mYSLdftolxGjVQ7uSSkC4kxZMZqBD
| dG3689QgB6XZM7ymvKXLXdUD1R/6H1XthZmmpYOwcGnD8hS8RGTcd1rVfUYnZGqQ
| Qxqn8qvPmdpOVMsPKgxuC3/XbP9bz29dDH51pVQy8QC6TZ1K6pMtIjF/hoz+LFxZ
| +k/hpI5AHExj3Hh3/zBQploDHXg7itQyc222+7B50JrsS/+Nnm2bXB9KNrJW4EDF
| 8xGHuA==
|_-----END CERTIFICATE-----
|_ssl-date: 2023-09-27T02:16:56+00:00; +2m28s from scanner time.
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
8000/tcp  open  http          syn-ack ttl 125 Apache httpd 2.4.54 ((Win64) OpenSSL/1.1.1p PHP/7.4.30)
|_http-server-header: Apache/2.4.54 (Win64) OpenSSL/1.1.1p PHP/7.4.30
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
|_http-open-proxy: Proxy might be redirecting requests
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.247.249:8000/dashboard/
47001/tcp open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 16327/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 47317/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 26089/udp): CLEAN (Failed to receive data)
|   Check 4 (port 50837/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 2m27s, deviation: 0s, median: 2m27s
| smb2-time: 
|   date: 2023-09-27T02:16:45
|_  start_date: N/A

Post-scan script results:
| clock-skew: 
|   2m28s: 
|     192.168.247.248
|     192.168.247.247
|_    192.168.247.249
```