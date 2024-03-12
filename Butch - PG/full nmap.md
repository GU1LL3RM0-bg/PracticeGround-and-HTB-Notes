
# TCP

```sh
PORT     STATE SERVICE       REASON  VERSION
21/tcp   open  ftp           syn-ack Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT

25/tcp   open  smtp          syn-ack Microsoft ESMTP 10.0.17763.1
| smtp-commands: butch Hello [192.168.45.216], TURN, SIZE 2097152, ETRN, PIPELINING, DSN, ENHANCEDSTATUSCODES, 8bitmime, BINARYMIME, CHUNKING, VRFY, OK
|_ This server supports the following commands: HELO EHLO STARTTLS RCPT DATA RSET MAIL QUIT HELP AUTH TURN ETRN BDAT VRFY

135/tcp  open  msrpc         syn-ack Microsoft Windows RPC

139/tcp  open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn

445/tcp  open  microsoft-ds? syn-ack

450/tcp  open  http          syn-ack Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Butch

5985/tcp open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: butch; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 19291/tcp): CLEAN (Timeout)
|   Check 2 (port 19577/tcp): CLEAN (Timeout)
|   Check 3 (port 34956/udp): CLEAN (Timeout)
|   Check 4 (port 52175/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2023-11-14T01:29:42
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: 0s

```
