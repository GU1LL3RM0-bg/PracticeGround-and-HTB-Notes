1. Server is running apache
2. Unexpected in root /swap.img       
3. Weird files in path:
```
/usr/bin/gettext.sh         
/usr/bin/rescan-scsi-bus.sh
```

```
PID:           781
Owner:         root
Program path:  /opt/apache2/bin/httpd
    Checking if anyone except root can change /opt/apache2/bin/httpd
WARNING: /opt/apache2/bin/httpd is currently running as root. The user apache can write to /opt/apache2/bin/httpd
WARNING: /opt/apache2/bin/httpd is currently running as root. The user apache can write to /opt/apache2/bin
WARNING: /opt/apache2/bin/httpd is currently running as root. The user apache can write to /opt/apache2
```

There are a ton of weird cron jobs:

```
/etc/cron.d:
total 20K
drwxr-xr-x  2 root root 4.0K Oct 12  2022 .
drwxr-xr-x 98 root root 4.0K Oct 28  2022 ..
-rw-r--r--  1 root root  201 Feb 14  2020 e2scrub_all
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder
-rw-r--r--  1 root root  191 Apr 23  2020 popularity-contest

/etc/cron.daily:
total 48K
drwxr-xr-x  2 root root 4.0K Oct 12  2022 .
drwxr-xr-x 98 root root 4.0K Oct 28  2022 ..
-rwxr-xr-x  1 root root  376 Dec  4  2019 apport
-rwxr-xr-x  1 root root 1.5K Apr  9  2020 apt-compat
-rwxr-xr-x  1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x  1 root root 1.2K Sep  5  2019 dpkg
-rwxr-xr-x  1 root root  377 Jan 21  2019 logrotate
-rwxr-xr-x  1 root root 1.1K Feb 25  2020 man-db
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x  1 root root 4.5K Jul 18  2019 popularity-contest
-rwxr-xr-x  1 root root  214 Apr  2  2020 update-notifier-common

/etc/cron.hourly:
total 12K
drwxr-xr-x  2 root root 4.0K Apr 23  2020 .
drwxr-xr-x 98 root root 4.0K Oct 28  2022 ..
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder

/etc/cron.monthly:
total 12K
drwxr-xr-x  2 root root 4.0K Apr 23  2020 .
drwxr-xr-x 98 root root 4.0K Oct 28  2022 ..
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder

/etc/cron.weekly:
total 20K
drwxr-xr-x  2 root root 4.0K Oct 12  2022 .
drwxr-xr-x 98 root root 4.0K Oct 28  2022 ..
-rwxr-xr-x  1 root root  813 Feb 25  2020 man-db
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x  1 root root  403 Apr 25  2022 update-notifier-common

```

