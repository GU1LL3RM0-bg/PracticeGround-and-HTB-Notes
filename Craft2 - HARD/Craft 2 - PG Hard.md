
Craft2 = 192.168.204.188

- System info
	- Windows 10 x64 bit, not a domain joined machine.

- Open TCP Ports
	- 80 = **Apache httpd 2.4.48** ((Win64) OpenSSL/1.1.1k **PHP/8.0.7**)
		- Gobuster interesting folders: **/uploads**
		- This page is expecting a resume upload, only accepts ODT files `http://craft/upload.php` = `File is not valid. Please submit ODT file`
	- 445
		- Tried anonymous log in but didn't work (credentials needed)
	- **49666**
		- Unknown port **????**

- Open UDP Ports:
	- No open UDP ports

### Usernames and Creds

- admin@craft.offs
- CRAFT2\\thecybergeek:**winniethepooh**
### Things to Try:

### Path to Compromise
1. Created a malicious ODT file that forced NTLM auth so we could steal a NTLM hash with responder. We did this since normal macros in ODT file was not working. To create the malicious ODT file to steal the hash, I used a python script from github that generates the odt file for you. See `https://github.com/rmdavy/badodf/blob/master/badodt.py`
2. Sent the malicious odt file as resume and stole the hash using responder.
3. Cracked the hash using hashcat mode 5600 for NTLMv2. **Password found: winniethepooh**
4. Checking the smb shares available, we can see we got access to the WebApp share which is the root path of the webserver. We uploaded a simple php backdoor and achieved RCE. ![[Pasted image 20240114180109.png]]
5. Uploaded a msfvenom rev shell and executed it to gain access as user apache.
6. To elevate our privilege as system, we found that the mysql service is running as SYSTEM and not as apache `sc qc mysql`. ![[Pasted image 20240114204608.png]]
8. Using chisel, we did a port forwarding for 3306 and for 80. This allowed us to connect to the database with default root and empty password which allowed us to run commands as system via the sql cmdline. The other way would be to access the sql query box in the /phpmyadmin on port 80 which is 403 forbiden from the outside but if we access port 80 from the inside using chisel it will allow the connection. ![[Pasted image 20240114204646.png]]
9. Since we can run sql queries as mysql which is running as SYSTEM, we can write files in C:\\windows\\system32 and abuse an arbitrary file write permissions vulnerability to escalate privs using the **WerTrigger** method or the **Diaghub** method, both of them explained in Payloads All the Things. Basically, we'll need to create our malicious dll using msfvenom, put the dll in system32 using sql query `select LOAD_FILE('c:\\path\\to\\malicious\\dll') into DUMPFILE 'c:\\windows\\system32\\malicious.dll';` and then trigger the execution by running the vulnerable binaries wertrigger or diaghub.


### WerTrigger Method - https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/windows-privilege-escalation/#wertrigger
1. upload malicious dll named as phoneinfo.dll and the required wertrigger files
![[Pasted image 20240114202927.png]]
2. Port FW 80 using chisel in order to access phpmyadmin from kali
![[Pasted image 20240114203351.png]]
![[Pasted image 20240114203245.png]]
3. Copy the malicious dll into system32
![[Pasted image 20240114203439.png]]
We can confirm the file copy was done successfully using dir

![[Pasted image 20240114203531.png]]
4. Start a nc listener and run wertrigger from the same location where Report.wer is located.

![[Pasted image 20240114203643.png]]
PWND!!!

### DiagHub Method - https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/windows-privilege-escalation/#diaghub

1. Create a malicious dll using msfvenom, I named it `test.dll` and move it into `C:\Windows\System32`.

![[Pasted image 20240114204221.png]]

1. Download diaghub.exe from the release section on `https://github.com/xct/diaghub`
2. Run diaghub and specify the malicious dll name `diaghub.exe c:\\ProgramData\\ test.dll`

![[Pasted image 20240114204333.png]]
![[Pasted image 20240114204351.png]]

