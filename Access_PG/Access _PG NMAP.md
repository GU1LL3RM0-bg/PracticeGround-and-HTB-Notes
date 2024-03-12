```sh

PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus

80/tcp    open  http          syn-ack Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-favicon: Unknown favicon MD5: FED84E16B6CCFE88EE7FFAAE5DFEFD34
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
| http-methods: 
|   Supported Methods: OPTIONS HEAD GET POST TRACE
|_  Potentially risky methods: TRACE
|_http-title: Access The Event

88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2023-11-21 01:54:09Z)

135/tcp   open  msrpc         syn-ack Microsoft Windows RPC

139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn

389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)

445/tcp   open  microsoft-ds? syn-ack

464/tcp   open  kpasswd5?     syn-ack

593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0

636/tcp   open  tcpwrapped    syn-ack

3268/tcp  open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)

3269/tcp  open  tcpwrapped    syn-ack

5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found

9389/tcp  open  mc-nmf        syn-ack .NET Message Framing

49666/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49669/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         syn-ack Microsoft Windows RPC
49673/tcp open  msrpc         syn-ack Microsoft Windows RPC
49695/tcp open  msrpc         syn-ack Microsoft Windows RPC
49734/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: SERVER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-11-21T01:54:58
|_  start_date: N/A
|_clock-skew: 0s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 14774/tcp): CLEAN (Timeout)
|   Check 2 (port 10294/tcp): CLEAN (Timeout)
|   Check 3 (port 64906/udp): CLEAN (Timeout)
|   Check 4 (port 41470/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked


```