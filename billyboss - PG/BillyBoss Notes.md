
This is not a domain joined machine.

FTP requires SSL. Not useful so far.

#### Initial Access - CVE-2020-10199 ( Sonatype Nexus 3.21.1 - Remote Code Execution (Authenticated))

Doing magic we needed to guess that the password for this service was nexus:nexus. With valid credentials, we were able to achieve RCE exploiting CVE-2020-10199.

I created a encoded powershell reverse shell and paste it in the payload field in the exploit. Then I was able to catch a reverse shell

![[Pasted image 20231102214939.png]]

#### Privilege Escalation

Powershell History:
```
PS C:\BaGet> cat C:\Users\nathan\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

cd \users\nathan\desktop
set-executionpolicy unrestricted
.\build.ps1
cd \users\nathan\desktop
.\build.ps1

```

### SharpUp.exe

```
=== Modifiable Service Binaries ===
        Service 'Sonatype Nexus' (State: Running, StartMode: Auto) : "C:\Users\nathan\Nexus\nexus-3.21.0-05\bin\nexus.exe"

```

I replaced the nexus.exe binary with a msfvenom reverse TCP shell and rebooted the machine. However, no privilege escalation was obtained since the service was running as user nathan.

Next, I checked nathan privilege and found he had SeImpersonatePrivilege. I used GodPotato exploit to run `nc.exe -e cmd.exe` as SYSTEM and was able to catch a reverse shell.




![[Pasted image 20231102223043.png]]