### Computers

```
dnshostname        operatingsystem              operatingsystemversion
-----------        ---------------              ----------------------
DC02.relia.com     Windows Server 2022 Standard 10.0 (20348)          
MAIL.relia.com     Windows Server 2022 Standard 10.0 (20348)          
login.relia.com    Windows Server 2022 Standard 10.0 (20348)          
WK01.relia.com     Windows 11 Pro               10.0 (22000)          
WK02.relia.com     Windows 11 Pro               10.0 (22000)          
INTRANET.relia.com Windows Server 2022 Standard 10.0 (20348)          
FILES.relia.com    Windows Server 2022 Standard 10.0 (20348)          
WEBBY.relia.com    Windows Server 2022 Standard 10.0 (20348)
```
### Users
``
```
samaccountname memberof                                                                                                
-------------- --------                                                                                                
Administrator  {CN=Group Policy Creator Owners,CN=Users,DC=relia,DC=com, CN=Domain Admins,CN=Users,DC=relia,DC=com, ...
Guest          CN=Guests,CN=Builtin,DC=relia,DC=com                                                                    
krbtgt         CN=Denied RODC Password Replication Group,CN=Users,DC=relia,DC=com                                      
maildmz                                                                                                                
jim                                                                                                                    
michelle       CN=INTRANETRDP,CN=Users,DC=relia,DC=com                                                                 
andrea         CN=WKRDP,CN=Users,DC=relia,DC=com                                                                       
mountuser                                                                                                              
iis_service                                                                                                            
internaladmin  CN=Domain Admins,CN=Users,DC=relia,DC=com                                                               
larry          CN=WEBBYADMINS,CN=Users,DC=relia,DC=com                                                                 
jenny          {CN=ITADMINS,CN=Users,DC=relia,DC=com, CN=Backup Operators,CN=Builtin,DC=relia,DC=com}                  
brad                                                                                                                   
anna                                                                                                                   
dan            CN=Domain Admins,CN=Users,DC=relia,DC=com                                                               
milana                                                   
```

### Groups

Admin Groups:
```
samaccountname       
--------------       
Schema Admins        
Enterprise Admins    
Domain Admins        
Key Admins           
Enterprise Key Admins
DnsAdmins            
WEBBYADMINS      <-- !!!!!!!!!!!!
ITADMINS         <-- !!!!!!!!
```

Non admin but interesting groups:
```
WKRDP                                   
INTRANETRDP                             
WEBBYADMINS                             
ITADMINS   
```

### Share Access from jim's 
```
Find-DomainShare -CheckShareAccess

Name           Type Remark              ComputerName  
----           ---- ------              ------------  
ADMIN$   2147483648 Remote Admin        WK01.relia.com
C$       2147483648 Default share       WK01.relia.com
NETLOGON          0 Logon server share  DC02.relia.com
SYSVOL            0 Logon server share  DC02.relia.com
apps              0                     FILES.relia...
monit...          0                     FILES.relia...
scripts           0                     FILES.relia...
```