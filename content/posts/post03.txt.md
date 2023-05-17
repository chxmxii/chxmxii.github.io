---
author:
  name: "chxmxii_"
date: 2023-05-14
tags:
  - DevOps
  - Sysadmin
type:
- post
- posts
title: KodeKloud Engineer Challenges
weight: 10
series:
- Hugo 101
---


## Linux TimeZones Setting	

+ During the daily standup, it was pointed out that the timezone across Nautilus Application Servers in Stratos Datacenter doesn't match with that of the local datacenter's timezone, which is America/Blanc-Sablon.

###### solution : 
+ 
  ```shell
  #ssh to app server 1 account and switch to root
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  sudo su -
  # change the timezone to America/Blanc-Sablon
  timedatectl set-timezone America/Blanc-Sablon
  #Verify
  timedatectl
  ```
---
## Linux User Files

+ There was some users data copied on Nautilus App Server 1 at /home/usersdata location by the Nautilus production support team in Stratos DC. Later they found that they mistakenly mixed up different user data there. Now they want to filter out some user data and copy it to another location. Find the details below:

+ On App Server 1 find all files (not directories) owned by user javed inside /home/usersdata directory and copy them all while keeping the folder structure (preserve the directories path) to /news directory.

###### Solution : 
+ 
  ```shell
  #ssh to user tony on App server 1 
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #find all the files copy the to /news directory while keeping the folder structure.
  find /home/usersdata/ -type f -user javed -exec cp --parents {} /news/ \; 2>/dev/null
  #verify
  yum install tree -y
  tree /news
  ```
---
## Linux User Without Home 

+ The system admins team of xFusionCorp Industries has set up a new tool on all app servers, as they have a requirement to create a service user account that will be used by that tool. They are finished with all apps except for App Server 1 in Stratos Datacenter.

+ Create a user named ravi in App Server 1 without a home directory.

###### Solution:
+ 
  ```shell
  #Login to tony account in App server 1 via SSH
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #man useradd and look for the option to create a user without a home dir
  sudo useradd -M ravi
  #verify
  getent passwd
  ```
---
## MariaDB Troubleshooting	

  + There is a critical issue going on with the Nautilus application in Stratos DC. The production support team identified that the application is unable to connect to the database. After digging into the issue, the team found that mariadb service is down on the database server.
  + Look into the issue and fix the same.

###### Soltuion:
+ 
  ``` shell
  #Connect to the db instance
  sshpass -p Sp\!dy ssh -o StrictHostKeyChecking=no peter@stdb01
  #Verify the status of mariadb service
  systemctl status mariadb
  #go through the logs and pay some attention!
  journalctl -xe
  cat /var/log/mariadb/mariadb.log
  #as you can see, the mariadb service coudln\'t start due to 'permision denied' problem! 
  #lets check the ownerships of both folders /var/lib/mysql and /var/run/mariadb
  ll /var/lib/mysql
  ll /var/run/mariadb
  #we notice that the owner of mariadb is sat to root and this was the root problem
  cd /var/run/
  chown -R mysql:mysql mariadb/
  #start and enbale the service
  systemctl start mariadb
  systemctl enable --now mariadb
  ```
---
## Linux SSH Authentication	

+ The system admins team of xFusionCorp Industries has set up some scripts on jump host that run on regular intervals and perform operations on all app servers in Stratos Datacenter. To make these scripts work properly we need to make sure the thor user on jump host has password-less SSH access to all app servers through their respective sudo users (i.e tony for app server 1). Based on the requirements, perform the following:

+ Set up a password-less authentication from user thor on jump host to all app servers through their respective sudo users.

###### Solution: 
+ 
  ```shell
  #generate ssh key
  ssh-keygen -t rsa 
  #copy the generated key
  ssh-copy-id tony@stapp01
  ssh-copy-id steve@stapp02
  ssh-copy-id banner@stapp03
  #verify
  ssh tony@stapp01
  ssh steve@stapp02
  ssh banner@stapp03
  ``` 
---
## Linux Collaborative Directories

+ The Nautilus team doesn't want its data to be accessed by any of the other groups/teams due to security reasons and want their data to be strictly accessed by the devops group of the team.
+ Setup a collaborative directory /devops/data on Nautilus App 3 server in Stratos Datacenter.
+ The directory should be group owned by the group devops and the group should own the files inside the directory. The directory should be read/write/execute to the group owners, and others should not have any access.

+ 
  ```Shell
  #ssh to app server3
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  #switch to root
  sudo su -
  #create dir /devops/data
  mkdir -p /devops/data
  #change the group owner
  m1 : chgrp -R devops /devops && chgrp -R devops /devops/data (-R > for recursive)
  m2 : chown -R :devops /devops 
  #change the dir perms
  chmod -R 2770 /devops/ && chmod -R 2770 /devops/data
  2 > SGID
  7 > rwx
  0 > ---
  ```