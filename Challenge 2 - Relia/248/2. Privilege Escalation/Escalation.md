Initial foothold was gained via RDP after getting emma's creds by cracking keepass master passwords.

#### Local Users
```
User accounts for \\EXTERNAL

-------------------------------------------------------------------------------
Administrator            DefaultAccount           emma
Guest                    mark                     WDAGUtilityAccount
The command completed successfully.
```

```
C:\Users\emma>net localgroup "Remote Desktop Users"
Alias name     Remote Desktop Users
Comment        Members in this group are granted the right to logon remotely

Members

-------------------------------------------------------------------------------
emma
The command completed successfully.
```

### Emma info

```
USER INFORMATION
----------------

User Name     SID
============= ==============================================
external\emma S-1-5-21-4009087542-2049691914-1078741817-1001


GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes
====================================== ================ ============ ==================================================
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Desktop Users           Alias            S-1-5-32-555 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\REMOTE INTERACTIVE LOGON  Well-known group S-1-5-14     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

#### Local Admins
```
C:\Users\emma>net localgroup Administrators
Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
mark
The command completed successfully.
```


## Found local admin credentials for mark in a environment variable "AppKey"
```shell
Z% Check for some passwords or keys in the env variables 
    ComSpec: C:\Windows\system32\cmd.exe
    DriverData: C:\Windows\System32\Drivers\DriverData
    OS: Windows_NT
    Path: C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Program Files (x86)\Microsoft SQL Server\150\Tools\Binn\;C:\Program Files\Microsoft SQL Server\150\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\150\DTS\Binn\;C:\Program Files\Microsoft SQL Server\150\DTS\Binn\;C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\170\Tools\Binn\
    PATHEXT: .COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC
    PROCESSOR_ARCHITECTURE: AMD64
    PSModulePath: C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules;C:\Program Files (x86)\Microsoft SQL Server\150\Tools\PowerShell\Modules\
    TEMP: C:\Windows\TEMP
    TMP: C:\Windows\TEMP
    USERNAME: SYSTEM
    windir: C:\Windows
    NUMBER_OF_PROCESSORS: 2
    PROCESSOR_LEVEL: 23
    PROCESSOR_IDENTIFIER: AMD64 Family 23 Model 1 Stepping 2, AuthenticAMD
    PROCESSOR_REVISION: 0102
    AppKey: !8@aBRBYdb3!
```



### Manual Enum - Password hunting

```
    Directory: C:\users\All Users\Microsoft\Diagnosis


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/28/2023  10:42 PM             10 osver.txt
```

```
    Directory: C:\users\Administrator\AppData\Local\Microsoft\Edge\User Data\ZxcvbnData\3.0.0.0


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          3/8/2022   7:49 PM         307015 english_wikipedia.txt
-a----          3/8/2022   7:49 PM          30420 female_names.txt
-a----          3/8/2022   7:49 PM           7656 male_names.txt
-a----          3/8/2022   7:49 PM         271951 passwords.txt
-a----          3/8/2022   7:49 PM          86077 surnames.txt
-a----          3/8/2022   7:49 PM         183450 us_tv_and_film.txt
```

