```
PORT     STATE SERVICE            REASON  VERSION
21/tcp   open  ftp                syn-ack zFTPServer 6.0 build 2011-10-17
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| total 9680
| ----------   1 root     root      5610496 Oct 18  2011 zFTPServer.exe
| ----------   1 root     root           25 Feb 10  2011 UninstallService.bat
| ----------   1 root     root      4284928 Oct 18  2011 Uninstall.exe
| ----------   1 root     root           17 Aug 13  2011 StopService.bat
| ----------   1 root     root           18 Aug 13  2011 StartService.bat
| ----------   1 root     root         8736 Nov 09  2011 Settings.ini
| dr-xr-xr-x   1 root     root          512 Nov 05 09:09 log
| ----------   1 root     root         2275 Aug 08  2011 LICENSE.htm
| ----------   1 root     root           23 Feb 10  2011 InstallService.bat
| dr-xr-xr-x   1 root     root          512 Nov 08  2011 extensions
| dr-xr-xr-x   1 root     root          512 Nov 08  2011 certificates
|_dr-xr-xr-x   1 root     root          512 Jan 23  2023 accounts


242/tcp  open  http               syn-ack Apache httpd 2.2.21 ((Win32) PHP/5.3.8)
|_http-server-header: Apache/2.2.21 (Win32) PHP/5.3.8
| http-auth: 
| HTTP/1.1 401 Authorization Required\x0D
|_  Basic realm=Qui e nuce nuculeum esse volt, frangit nucem!
|_http-title: 401 Authorization Required
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS


3145/tcp open  zftp-admin         syn-ack zFTPServer admin


3389/tcp open  ssl/ms-wbt-server? syn-ack
|_ssl-date: 2023-11-05T02:13:22+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=LIVDA
| Issuer: commonName=LIVDA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2023-01-22T09:37:27
| Not valid after:  2023-07-24T09:37:27
| MD5:   ac37:df9e:5a8a:ddc9:6255:9d86:59a7:4d9d
| SHA-1: c75c:3e6a:277f:cfd6:1259:c4c7:4f4d:ea08:8061:a9d4
| -----BEGIN CERTIFICATE-----
| MIICzTCCAbWgAwIBAgIPb5ZLhJYto0k7+/F+xEcBMA0GCSqGSIb3DQEBBQUAMBAx
| DjAMBgNVBAMTBUxJVkRBMB4XDTIzMDEyMjA5MzcyN1oXDTIzMDcyNDA5MzcyN1ow
| EDEOMAwGA1UEAxMFTElWREEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIB
| AQCZmrw/ZiQpfKIo4Dk+5VN3qHwgsGqWQrRM3DjEmQ5/FPiDM1nSDXjrZhbkF/MC
| u4xELf03qRq6I8ZKQNDe3WInq2FTliTM4MC3N74QHxMtTM59+jCo3a0Q/VL8Y7OB
| F2VjuxrIVXOwrqXpWKlvE1bXJE/XjVaFOaGIAxsvYTpgYQOrey6rDaC4kGha0uo6
| 8lNJ2x3DZQEkWWZj0CTDKR5qsP6/w5mdY0w4mIy7ATPRRPpXEi4R1Apj5XgBSJJ0
| rFWNMUSe0SQJlBcPZpEg//iUD6yDNyydI0BrFuB25JllF7KyZrW9Zf2FIkS2X/C8
| Sm8+Mm1v3Gu1lQQNB5vmKbsTAgMBAAGjJDAiMBMGA1UdJQQMMAoGCCsGAQUFBwMB
| MAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQUFAAOCAQEABu0Fdl5zY69+rU24pjwS
| Om4Am7jqYQFoF8z2sB8pZfoV2Cah6JZ3A+3AhEDvBBKVDx7aKXCsngJSP3c2Xkb+
| f5NxuoAtVwLTojgTwoxv5H2hxJU4eQgdW+B12R1oq7eEeaElb1uo/m7RXW7ZVg9X
| omH89nWjz/VfhDVBEH1C3tDakFEZ5+UostJ5qMhw8rdNPYhz4mwkokq/SgxpQwDq
| tU86Iu4y1ikL2WXBZ7nEQAEa/8WgBJbkZ1V/FSkWJJKQWlIlXJFhRvuVm7w1q9LK
| 6J/KBo196wpI/5HYT0fgUMG7T3RPl787nYz5P86jmWGVPjalRXNgIdYVl5nWpZCk
| aA==
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: LIVDA
|   NetBIOS_Domain_Name: LIVDA
|   NetBIOS_Computer_Name: LIVDA
|   DNS_Domain_Name: LIVDA
|   DNS_Computer_Name: LIVDA
|   Product_Version: 6.0.6001
|_  System_Time: 2023-11-05T02:13:18+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -2s

```