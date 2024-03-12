Can connect using anonymous but none of the files is accessible to me.

Checking the folder "Accounts" we can see 3 potential user acccounts:

```
Offsec
anonymous
admin
```

Using hydra against these users, we found that `admin:admin` is valid. Which allows us to download the `.htpasswd` password from the ftp server.

This file contains a password that we can crack.

![[Pasted image 20231104225347.png]]
```
offsec:$apr1$oRfRsc/K$UpYpplHDlaemqseM39Ugg0
```

```
Host memory required for this attack: 2858 MB

Dictionary cache hit:
* Filename..: rockyou.txt
* Passwords.: 14344388
* Bytes.....: 139921521
* Keyspace..: 14344388

$apr1$oRfRsc/K$UpYpplHDlaemqseM39Ugg0:elite

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1600 (Apache $apr1$ MD5, md5apr1, MD5 (APR))
Hash.Target......: $apr1$oRfRsc/K$UpYpplHDlaemqseM39Ugg0
Time.Started.....: Sat Nov 04 22:58:52 2023, (1 sec)
Time.Estimated...: Sat Nov 04 22:58:53 2023, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1689.0 kH/s (4.18ms) @ Accel:32 Loops:250 Thr:32 Vec:1
Speed.#2.........:        0 H/s (20.31ms) @ Accel:40 Loops:1 Thr:128 Vec:1
Speed.#*.........:  1689.0 kH/s
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 30720/14344388 (0.21%)
Rejected.........: 0/30720 (0.00%)
Restore.Point....: 0/14344388 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:750-1000
Restore.Sub.#2...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: acorp -> 000013
Candidates.#2....: 000011 -> 130497
Hardware.Mon.#1..: Temp: 58c Util:  1% Core:1974MHz Mem:6994MHz Bus:8
Hardware.Mon.#2..: N/A

Started: Sat Nov 04 22:58:28 2023
Stopped: Sat Nov 04 22:58:54 2023
```

### Initial Access

I logged into the web server 242 using `offsec:elite`

I was able to upload a php reverse shell from ivan (which works on windows too) and got initial access that way.

Note: I tried several msfvenom, powershell and nc.exe rev shell but none of them worked, simply because I DIDN'T check the OS version and I was trying to run x64 payloads on a 32 bit system :'D `ñame` 

---
### Priv Esc

For privilege escalation, I grabbed the info from `systeminfo` and ran it through the windows exploit suggester. I filter only for privilege escalation exploit with known available exploit. I ended using https://www.exploit-db.com/exploits/40564 (MS11-046) which had a compiled exploit binary already compiled in secWiki/windows-kernel-exploit github repo :D

![[Pasted image 20231105002542.png]]

![[Pasted image 20231105002559.png]]

![[Pasted image 20231105002526.png]]
