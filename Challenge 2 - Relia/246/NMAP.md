```
PORT     STATE SERVICE  VERSION
80/tcp   open  http     Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Code Validation
|_http-server-header: Apache/2.4.52 (Ubuntu)
443/tcp  open  ssl/http Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.52 (Ubuntu)
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=demo
| Subject Alternative Name: DNS:demo
| Issuer: commonName=demo
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-10-12T07:46:27
| Not valid after:  2032-10-09T07:46:27
| MD5:   6361:be08:5259:3a75:cd26:f869:1614:3c94
|_SHA-1: 8fa0:04a7:5d03:4c29:44b7:6b14:119f:fd79:3c7e:5093
|_http-title: Code Validation
2222/tcp open  ssh      OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 42:2d:8d:48:ad:10:dd:ff:70:25:8b:46:2e:5c:ff:1d (ECDSA)
|_  256 aa:4a:c3:27:b1:19:30:d7:63:91:96:ae:63:3c:07:dc (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

### Port 2222 is accessible by anita

