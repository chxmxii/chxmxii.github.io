---
author:
  name: "chxmxii_"
date: 2022-04-20
tags:
  - ctf
  - sysadmin
linktitle: 
type:
- post
- posts
title: RingZer0CTF Sysadmin Track
weight: 10
series:
- Hugo 101
---

## SysAdmin Part 1
```Shell
┌──(kali㉿kali)-[~]
└─$ sshpass -p VNZDDLq2x9qXCzVdABbR1HOtz ssh -o StrictHostKeyChecking=no morpheus@challenges.ringzer0team.com -p 10089
Warning: Permanently added '[challenges.ringzer0team.com]:10089' (ED25519) to the list of known hosts.
 888888ba  oo                   d8888888P                    a8888a  d888888P                              
 88     8b                           .d8'                   d8    8b    88                                 
 88aaaa8P  dP 88d888b. .d8888b.    .d8'   .d8888b. 88d888b. 88  P 88    88    .d8888b. .d8888b. 88d8b.d8b. 
 88    8b. 88 88    88 88    88  .d8'     88ooood8 88    88 88 d  88    88    88ooood8 88    88 88  88  88 
 88     88 88 88    88 88    88 d8'       88.  ... 88       Y8    8P    88    88.  ... 88    88 88  88  88 
 dP     dP dP dP    dP `8888P88 Y8888888P `88888P' dP        Y8888P     dP    `88888P' `88888P8 dP  dP  dP 
oooooooooooooooooooooooo     88 ooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo
                        d8888P                                                                             

                                    +---------------------------------+
                                    |  Welcome to the Sysadmin track  |
                                    |                                 |
                                    |   - Play nice and play Fair -   |
                                    |                                 |
                                    |----[ info@ringzer0team.com ]----|
$ ls
```
Maybe something is there?
```Shell
$ ls -la
total 20
drwx------ 2 morpheus morpheus 4096 Apr 23  2022 .
drwxr-xr-x 9 root     root     4096 Apr 23  2022 ..
lrwxrwxrwx 1 root     root        9 Apr 23  2022 .bash_history -> /dev/null
-rwx------ 1 morpheus morpheus  220 Feb 25  2020 .bash_logout
-rwx------ 1 morpheus morpheus 3771 Feb 25  2020 .bashrc
-rwx------ 1 morpheus morpheus  807 Feb 25  2020 .profile
``` 
Checking /etc/passwd
```Shell
$ cat /etc/passwd
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ubuntu:x:1000:1002:Ubuntu:/home/ubuntu:/bin/bash
mysql:x:106:112:MySQL Server,,,:/nonexistent:/bin/false
sshd:x:107:65534::/run/sshd:/usr/sbin/nologin
morpheus:x:1001:1004:,666,666-6666,:/home/morpheus:/bin/sh
trinity:x:1002:1005::/home/trinity:/bin/bash
architect:x:1003:1006::/home/architect:/bin/bash
oracle:x:1004:1007::/home/oracle:/bin/bash
neo:x:1005:1008::/home/neo:/bin/bash
cypher:x:1006:1009::/home/cypher:/bin/bash
```
Nothing there, lets check for the running proccess!
```Shell
$ ps ax
    PID TTY      STAT   TIME COMMAND
      1 ?        Ss    98:57 /sbin/init splash
      2 ?        S      0:11 [kthreadd]
    209 ?        S      0:26 [hwrng]
    246 ?        S<s  204:07 /lib/systemd/systemd-journald
    285 ?        Ssl   76:40 /run/lxd_agent/lxd-agent
    290 ?        Ss    25:16 /lib/systemd/systemd-udevd
    317 ?        Ssl    4:32 /lib/systemd/systemd-timesyncd
    319 ?        I<     0:00 [ttm_swap]
    387 ?        Ss    10:41 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
    393 ?        Ss     0:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
    396 ?        S     12:54 /bin/sh /root/backup.sh -u trinity -p Flag-7e0cfcf090a2fe53c97ea3edd3883d0d
    406 ?        Ss    10:40 /lib/systemd/systemd-logind
1581771 ?        S      0:00 sleep 10
1581772 pts/0    R+     0:00 ps ax
```
+ Gottcha ya! -> Flag-7e0cfcf090a2fe53c97ea3edd3883d0d
---
## SysAdmin Part 2
 