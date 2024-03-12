1. Domain is relia.com
2. Found 2 usernames from metadata contained in pdfs exposed in 247
3. Login server exposed on 191
4. Email server exposed on 189
5. Found login page in development exposed in 245. Potential SQLi.
6. Potentially vulnerable CMS found in 248. DNN cms + login page.
7. Found 3 exposed SMB shares on 248 (IPC, transfer, users)
***8. Found kee pass database files exposed in open share on 248.***
9. Obtained valid domain credentials for user Emma as well as other set of credentials that i need to confirm later against the rest of the hosts.
10. Found potentially vulnerable CMS exposed on 249. RiteCMS.
11. Potential file inclusion on DNN cms on 248 via a writable share
12. Mark is localadmin on EXTERNAL 248. Mark's creds were found in an environment variable in 248 after running winpeas.
13. Found default credentials for RiteCMS which got us access to the admin portal on 249:8000/CMS/admin.php
14. Found cleartext password for user *damon* on LEGACY server via powershell history. Fortunately for us, user damon is local admin on .249
15. On 245, that version of apache (2.4.49) contains a vulnerabiltiy that allows directory traversal. This way, we were able to obtained a list of users on 245 and anita's ssh private key (id_ecdsa).
16. Cleartext credential for the administrator account were found in a files share \\files\monitoring. 
17. ![[Pasted image 20230930194245.png]]
18. 

