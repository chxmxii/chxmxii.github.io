---
author:
  name: "chxmxii_"
date: 2021-04-20
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
      3 ?        I<     0:00 [rcu_gp]
      4 ?        I<     0:00 [rcu_par_gp]
      6 ?        I<     0:00 [kworker/0:0H-kblockd]
      8 ?        I<     0:00 [mm_percpu_wq]
      9 ?        S     27:33 [ksoftirqd/0]
     10 ?        I     54:04 [rcu_sched]
     11 ?        S      2:11 [migration/0]
     12 ?        S      0:00 [idle_inject/0]
     14 ?        S      0:00 [cpuhp/0]
     15 ?        S      0:00 [kdevtmpfs]
     16 ?        I<     0:00 [netns]
     17 ?        S      0:00 [rcu_tasks_kthre]
     18 ?        S      0:00 [kauditd]
     19 ?        S      2:11 [khungtaskd]
     20 ?        S      0:00 [oom_reaper]
     21 ?        I<     0:01 [writeback]
     22 ?        S      0:00 [kcompactd0]
     23 ?        SN     0:00 [ksmd]
     24 ?        SN     2:20 [khugepaged]
     70 ?        I<     0:00 [kintegrityd]
     71 ?        I<     0:00 [kblockd]
     72 ?        I<     0:00 [blkcg_punt_bio]
     73 ?        I<     0:00 [tpm_dev_wq]
     74 ?        I<     0:00 [ata_sff]
     75 ?        I<     0:00 [md]
     76 ?        I<     0:00 [edac-poller]
     77 ?        I<     0:00 [devfreq_wq]
     78 ?        S      0:00 [watchdogd]
     81 ?        S     12:06 [kswapd0]
     82 ?        S      0:00 [ecryptfs-kthrea]
     84 ?        I<     0:00 [kthrotld]
     85 ?        S      0:00 [irq/24-aerdrv]
     86 ?        S      0:00 [irq/25-aerdrv]
     87 ?        S      0:00 [irq/26-aerdrv]
     88 ?        S      0:00 [irq/27-aerdrv]
     89 ?        S      0:00 [irq/28-aerdrv]
     90 ?        S      0:00 [irq/29-aerdrv]
     91 ?        S      0:00 [irq/30-aerdrv]
     92 ?        S      0:00 [irq/31-aerdrv]
     93 ?        S      0:00 [irq/32-aerdrv]
     94 ?        I<     0:00 [acpi_thermal_pm]
     95 ?        I<     0:00 [vfio-irqfd-clea]
     98 ?        I<     0:00 [ipv6_addrconf]
    108 ?        I<     0:00 [kstrp]
    111 ?        I<     0:00 [kworker/u129:0]
    126 ?        I<     0:00 [charger_manager]
    160 ?        S      0:00 [scsi_eh_0]
    161 ?        I<     0:00 [scsi_tmf_0]
    162 ?        I<     5:08 [kworker/0:1H-kblockd]
    163 ?        S      0:00 [scsi_eh_1]
    164 ?        I<     0:00 [scsi_tmf_1]
    165 ?        S      0:00 [scsi_eh_2]
    166 ?        I<     0:00 [scsi_tmf_2]
    167 ?        S      0:00 [scsi_eh_3]
    168 ?        I<     0:00 [scsi_tmf_3]
    170 ?        S      0:00 [scsi_eh_4]
    171 ?        I<     0:00 [scsi_tmf_4]
    172 ?        S      0:00 [scsi_eh_5]
    173 ?        I<     0:00 [scsi_tmf_5]
    174 ?        S      0:00 [scsi_eh_6]
    175 ?        I<     0:00 [scsi_tmf_6]
    197 ?        S     22:24 [jbd2/sda2-8]
    198 ?        I<     0:00 [ext4-rsv-conver]
    209 ?        S      0:26 [hwrng]
    246 ?        S<s  204:07 /lib/systemd/systemd-journald
    285 ?        Ssl   76:40 /run/lxd_agent/lxd-agent
    290 ?        Ss    25:16 /lib/systemd/systemd-udevd
    317 ?        Ssl    4:32 /lib/systemd/systemd-timesyncd
    319 ?        I<     0:00 [ttm_swap]
    323 ?        I<     0:00 [cryptd]
    352 ?        Ss    17:37 /lib/systemd/systemd-networkd
    355 ?        Ss     5:05 /lib/systemd/systemd-resolved
    386 ?        Ss     5:58 /usr/sbin/cron -f
    387 ?        Ss    10:41 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
    393 ?        Ss     0:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
    396 ?        S     12:54 /bin/sh /root/backup.sh -u trinity -p Flag-7e0cfcf090a2fe53c97ea3edd3883d0d
    406 ?        Ss    10:40 /lib/systemd/systemd-logind
    420 ?        Ss   110:18 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
    433 ttyS0    Ss+    0:00 /sbin/agetty -o -p -- \u --keep-baud 115200,38400,9600 ttyS0 vt220
    451 tty1     Ss+    0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
    463 ?        Ssl    0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
  31638 ?        Ssl    6:10 /usr/lib/policykit-1/polkitd --no-debug
 209006 ?        Ss     3:46 /lib/systemd/systemd --user
 209007 ?        S      0:00 (sd-pam)
 824643 ?        Ssl   17:38 /usr/sbin/rsyslogd -n -iNONE
1581105 ?        I      0:02 [kworker/0:4-events]
1581227 ?        I      0:00 [kworker/u128:2-events_unbound]
1581319 ?        I      0:01 [kworker/0:0-mm_percpu_wq]
1581347 ?        I      0:00 [kworker/u128:1-events_unbound]
1581524 ?        I      0:00 [kworker/u128:3-events_unbound]
1581528 ?        Ss     0:00 sshd: morpheus [priv]
1581538 ?        Ss     0:00 /lib/systemd/systemd --user
1581539 ?        S      0:00 (sd-pam)
1581547 ?        S      0:00 sshd: morpheus@pts/0
1581549 pts/0    Ss     0:00 -sh
1581601 ?        I      0:00 [kworker/0:1-cgroup_destroy]
1581605 ?        I      0:00 [kworker/0:2-events]
1581679 ?        S      0:00 su neo -c /bin/monitor
1581681 ?        I      0:00 [kworker/u128:0-events_unbound]
1581682 ?        I      0:00 [kworker/0:3]
1581685 ?        Ss     0:00 /bin/monitor
1581715 ?        Ssl    0:07 /usr/sbin/mysqld
1581771 ?        S      0:00 sleep 10
1581772 pts/0    R+     0:00 ps ax
```
Gottcha ya! - <Flag-7e0cfcf090a2fe53c97ea3edd3883d0d>