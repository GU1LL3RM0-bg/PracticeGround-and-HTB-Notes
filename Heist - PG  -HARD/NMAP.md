```
Nmap scan report for 192.168.198.165
Host is up, received user-set (0.035s latency).
Scanned at 2023-11-06 19:47:13 EST for 205s
Not shown: 65513 filtered tcp ports (no-response)
PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus

88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2023-11-07 00:49:07Z)

135/tcp   open  msrpc         syn-ack Microsoft Windows RPC

139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn

389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: heist.offsec0., Site: Default-First-Site-Name)

445/tcp   open  microsoft-ds? syn-ack

464/tcp   open  kpasswd5?     syn-ack

593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0

636/tcp   open  tcpwrapped    syn-ack

3268/tcp  open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: heist.offsec0., Site: Default-First-Site-Name)

3269/tcp  open  tcpwrapped    syn-ack

3389/tcp  open  ms-wbt-server syn-ack Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: HEIST
|   NetBIOS_Domain_Name: HEIST
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: heist.offsec
|   DNS_Computer_Name: DC01.heist.offsec
|   DNS_Tree_Name: heist.offsec
|   Product_Version: 10.0.17763
|_  System_Time: 2023-11-07T00:49:56+00:00
| ssl-cert: Subject: commonName=DC01.heist.offsec
| Issuer: commonName=DC01.heist.offsec
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-11-06T00:46:26
| Not valid after:  2024-05-07T00:46:26
| MD5:   2749:dd2d:82de:5ca3:d594:cf78:45ae:731f
| SHA-1: a02b:a731:38b2:132c:9b33:0276:5e2a:4b9f:d9ce:ab13
| -----BEGIN CERTIFICATE-----
| MIIC5jCCAc6gAwIBAgIQH5IQUW877YtGeDhbxTQ/YTANBgkqhkiG9w0BAQsFADAc
| MRowGAYDVQQDExFEQzAxLmhlaXN0Lm9mZnNlYzAeFw0yMzExMDYwMDQ2MjZaFw0y
| NDA1MDcwMDQ2MjZaMBwxGjAYBgNVBAMTEURDMDEuaGVpc3Qub2Zmc2VjMIIBIjAN
| BgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApt5A+azY7Y3mDiBYoIBH1OHtiJ+9
| Zdwv3ubToBxjH/qX62K3DOKNW3800Ocqd+/Vd/qEic7v784Opr2J8iKOrjMcAKXB
| 8zIbqkLFx7RMyNVql4d8DeaCI14YAaK1EwJCABkO29h5zV2pbw/PAwFRMnhKmWs2
| cK4iUaSN0p7elqaslt4KT/lhikHEXGHBd9ORU3tUqeExGWeAoxiLdYfT8T0XUPVY
| 5QjD3YVyBg90EYaIXrRTHNjngbiVjGF6oU3/ehYPpwQuTO5d+VqAUPeqsPT3qgvU
| pKuc0t1pXezPu5SIPPWCq1IKC7CZlYv7W3cVmnVBrddsy0oKHpyQaEnVrQIDAQAB
| oyQwIjATBgNVHSUEDDAKBggrBgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcN
| AQELBQADggEBAGUWf2Xk0RmcnhZEgtALi5fRR3wCibPgcMh1ZaHwY8AYt3L7vgY6
| SDg0Zx8h8PCoObK9g2NyvQwM47wl8wBOdbITrPS5q9mrq2rKKqZ25Fh1KirxmA9h
| kA+sF4veK21OaiA5tMAOW4onZL7GD0fO6Vz6itFgI/K4JNXwNSWj3NJxj9GCiKQG
| quctPkQClLZr7rXrZkYbe8K/+8bmnew/lFqbJKw36NNy/MxpWQloSSESzx9NHs0x
| tHYxj6avzjrodawtQb8Cj2mnIaQhdFRrpFONZmt9IhF6mYe9iXOopkc6eZt8kOQB
| V7R8X67oO3TpcjW0zL8XwsOoIdFEIT9Vuhc=
|_-----END CERTIFICATE-----
|_ssl-date: 2023-11-07T00:50:36+00:00; -1s from scanner time.

5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0

8080/tcp  open  http          syn-ack Werkzeug httpd 2.0.1 (Python 3.9.0)
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-server-header: Werkzeug/2.0.1 Python/3.9.0
|_http-title: Super Secure Web Browser

9389/tcp  open  mc-nmf        syn-ack .NET Message Framing

49665/tcp open  msrpc         syn-ack Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack Microsoft Windows RPC
49669/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         syn-ack Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack Microsoft Windows RPC
49696/tcp open  msrpc         syn-ack Microsoft Windows RPC
49711/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26385/tcp): CLEAN (Timeout)
|   Check 2 (port 14951/tcp): CLEAN (Timeout)
|   Check 3 (port 44477/udp): CLEAN (Timeout)
|   Check 4 (port 62569/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-time: 
|   date: 2023-11-07T00:50:00
|_  start_date: N/A
```