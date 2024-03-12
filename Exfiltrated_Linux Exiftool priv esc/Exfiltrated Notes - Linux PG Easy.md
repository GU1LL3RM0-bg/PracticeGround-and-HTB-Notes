

machine host name =exfiltrated.offsec
  
Attack Surface:
  80 = Powered by **Subrion 4.2.1** (apache)
    robots = http-robots.txt: 7 disallowed entries 
    	| /backup/ /cron/? /front/ /install/ /panel/ /tmp/ 
		|/updates/

  22 = ssh
  
Findings:
	Found Admin panel login @ exfiltrated.com/panel
	Found cron job running a script as root from the /opt folder.
  
Things To Try:

- Internal Port redirection to 3306
- Check cron job running something from /opt folder



## Path To Compromise

1. A vulnerable version of Subrion CMS 4.2.1 was found running on Port *80* which was vulnerable to **Authenticated RCE**
2. To exploit this, https://github.com/Swammers8/SubrionCMS-4.2.1-File-upload-RCE-auth-
3. For credentials, the CMS was using the defaults one **admin:admin**

**Priv Esc:**
1. I ran pspy64 and found that a cron job was running every minute a script located in /opt.
2. Reading the content of the script, it was grabbing all JPG files from the /uploads folder and then running the `exiftool` command against each JPG file found, in order to extrac metadata and save it.
3. The problem here is that some versions of **exiftool** **<12.24** are vulnerable and allow RCE if we are able to embed certain metadata in the image. 
4. I followed the steps in these tutorials to create a malicious hacker.jpg image that will take advantage of exiftool and run commands as root from the cron job.
	1. https://jayngng.github.io/blog/exfiltrated-ospg/
	2. https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/