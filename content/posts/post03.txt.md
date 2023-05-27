---
author:
  name: "chxmxii_"
date: 2023-05-26
tags:
  - DevOps
  - Sysadmin
type:
- post
- posts
title: KodeKloud Engineer System Administration Challenges 
weight: 10
series:
- Hugo 101
---
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
  
###### Solution
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
---
## Linux NTP Setup

+ The system admin team of xFusionCorp Industries has noticed an issue with some servers in Stratos Datacenter where some of the servers are not in sync w.r.t time. Because of this, several application functionalities have been impacted. To fix this issue the team has started using common/standard NTP servers. They are finished with most of the servers except App Server 1. Therefore, perform the following tasks on this server:
+ Install and configure NTP server on App Server 1.
+ Add NTP server 1.south-america.pool.ntp.org in NTP configuration on App Server 1.
+ Please do not try to start/restart/stop ntp service, as we already have a restart for this service scheduled for tonight and we don't want these changes to be applied right now
  
###### Solution:
+ 
  ```Shell
  #ssh to app server 1
  sshpass -p Ir0nM@n ssh -o StrictHostkeyChecking=no tony@stapp01
  #switch to root user
  sudo su -
  #Install ntp server if not installed
  rpm -qa | grep ntp
  yum install -y ntp
  #Configure NTP server
  vi /etc/ntp.conf 
  ~ insert this line (NTP server 1.south-america.pool.ntp.org) then save and quit
  #Start and enable the ntp daemon
  systemctl enable ntpd
  systemctl start ntpd
  systemctl status ntpd
  #verify configuration
  ntpstat
  ```
---
## Linux Run Levels	

+ New tools have been installed on the app server in Stratos Datacenter. Some of these tools can only be managed from the graphical user interface. Therefore, there are requirements for these app servers.
+ On all App servers in Stratos Datacenter change the default runlevel so that they can boot in GUI (graphical user interface) by default. Please do not try to reboot these servers

###### Solution:
+ 
  ```Shell
  #ssh to the app server of the 3 accounts
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  #switch user to root
  sudo su -
  #get the default target
  systemctl get-default
  #notice the default target is set to multi-user.target, you can list all the target using the following command
  systemctl list-units
  #set the default target to graphical.target
  systemctl set-default graphical.target
  #start the new target and verify
  systemctl start graphical.target && systemctl status graphical.target
  systemctl get-default
  ```
---
## Linux String Substitute

+ The backup server in the Stratos DC contains several template XML files used by the Nautilus application. However, these template XML files must be populated with valid data before they can be used. One of the daily tasks of a system admin working in the xFusionCorp industries is to apply string and file manipulation commands!
+ Replace all occurances of the string Sample to Software on the XML file /root/nautilus.xml located in the backup server.

###### Solution
+ ```Shell 
  #ssh to the backup server
  sshpass -p H@wk3y3 ssh -o StrictHostKeyChecking=no clint@stbkp01
  #switch to the root user
  sudo su -
  #change the word Sample with SoftWare in the XML file nautilus.xml
  cat /root/nautilus.xml
  #Using sed, you can always refer to the manual for help
  sed -i 's/Sample/Software/g' nautilus.xml
  #Breaking down the sed command
  -i > save the changes to the file --in-place
  s > substitue
  g > global
  #Using awk, refer to the manual for help
  awk '{gsub("Sample", "Software", $0); print > "nautilus.xml"}' nautilus.xml
  #Breaking down the awk command
  gsub() -> awk function to globally substitue the Sample word with Software
  $0 -> refers to the entire input line being proccessed (awk reads the input file line by line until it reaches the end of file).
  print > "nautilus.xml" -> overwrites the original file with the modified lines
  ```
---
## Linux String Substitute (sed)

+ There is some data on Nautilus App Server 1 in Stratos DC. Data needs to be altered in several of the files. On Nautilus App Server 1, alter the /home/BSD.txt file as per details given below:
+ a. Delete all lines containing word following and save results in /home/BSD_DELETE.txt file. (Please be aware of case sensitivity)
+ b. Replace all occurrence of word and to them and save results in /home/BSD_REPLACE.txt file.
+ Note: Let's say you are asked to replace word to with from. In that case, make sure not to alter any words containing this string; for example upto, contributor etc.

###### Solution: 
+ 
  ```Shell
  #ssh to app server 1
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #switch to roo user
  sudo su -
  #cd the /home dir and cat the BSD.txt
  cd /home 
  cat BSD.txt
  #Delete all lines containing word following and save results in /home/BSD_DELETE.txt file.
  sed '/\<following\>/d' /home/BSD.txt > /home/BSD_DELETE.txt
  #Here, \< and \> are word boundaries in regular expressions, ensuring that only the exact word "following" is 
  matched and not parts of other words.
  #Cat the original file and the modified one to verify
  cat BSD.txt | grep following 
  cat BSD_DELETE.txt | grep follwing
  #Replace all occurrence of word and to them and save results in /home/BSD_REPLACE.txt file.
  sed 's/\band\b/them/g' /home/BSD.txt > /home/BSD_REPLACE.txt
  #Here, \b represents word boundaries in regular expressions, ensuring that only the word "and" is matched as a standalone word and not parts of other   
  words. 
  #Cat the modified file to verify
  cat BSD_REPLACE.txt | grep them
  cat BSD_REPLACE.txt | grep them
  ``` 
---
## DNS TroubleShooting

+ the system admins team of xFusionCorp Industries has noticed intermittent issues with DNS resolution in several apps . App Server 3 in Stratos Datacenter is having some DNS resolution issues, so we want to add some additional DNS nameservers on this server.
+ As a temporary fix we have decided to go with Google public DNS (ipv4). Please make appropriate changes on this server.
ee
###### Solution

+ 
  ```Shell
  #Connect to the app server 3
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  #switch to root user
  sudo su -
  #test dns with ping command
  ping www.google.com
  ping 8.8.8.8
  #modify resolv.conf file
  vi /etc/resolv.conf
  #add the following lines
  nameserver 8.8.8.8
  nameserver 8.8.4.4
  #save modification and test
  ping www.google.com
  ping 8.8.8.8
  ```
---
## Selinux Installation	

+ The xFusionCorp Industries security team recently did a security audit of their infrastructure and came up with ideas to improve the application and server security. They decided to use SElinux for an additional security layer. They are still planning how they will implement it; however, they have decided to start testing with app servers, so based on the recommendations they have the following requirements:
+ Install the required packages of SElinux on App server 1 in Stratos Datacenter and disable it permanently for now; it will be enabled after making some required configuration changes on this host. Don't worry about rebooting the server as there is already a reboot scheduled for tonight's maintenance window. Also ignore the status of SElinux command line right now; the final status after reboot should be disabled.

###### Solution
+ 
  ```Shell 
  #ssh to app server1
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #switch to root user
  sudo su -
  #testing selinux
  setatus
  #installing selinux
  yum whatprovides selinux
  yum search selinux
  #install all the packages
  yum install policycoreutils policycoreutils-python selinux-policy selinux-policy-targeted libselinux-utils setroubleshoot-server setools setools-console mcstrans
  #check selinux status
  sestatus
  #set selinux mode to disabled
  vi /etc/selinux/config => Change the SELINUX=enforcing to SELINUX=disabled
  #checking selinux status again
  sestatus
  ```
---
## Create Cron Jon

+ The Nautilus system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in Stratos DC on a set schedule. Before that they need to test similar functionality with a sample cron job. Therefore, perform the steps below:
+ a. Install cronie package on all Nautilus app servers and start crond service.
+ b. Add a cron */5 * * * * echo hello > /tmp/cron_text for root user.
  
###### Solution:

+ 
  ```Shell
  #ssh to the app servers
  sshpass -p <password> ssh -o StrictHostKeyChecking=no <user>@stapp0{1,2,3}
  #switch to root user
  sudo su -
  #install cron service
  yum whatprovides crontab
  yum install cronie
  #start and enable the service
  systemctl start cronie
  systemctl enable --now cronie
  systemctl status cronie
  #create a new cron job and copy the job given, the -u here arg is for user and is not actually necessary here
  crontab -e -u root 
  #verify
  crontab -l
  cat /var/spool/cron/root
  ```
---
## Linux Remote Copy

+ One of the Nautilus developers has copied confidential data on the jump host in Stratos DC. That data must be copied to one of the app servers. Because developers do not have access to app servers, they asked the system admins team to accomplish the task for them.
+ Copy /tmp/nautilus.txt.gpg file from jump server to App Server 1 at location /home/webapp.

###### Solution:
+ ```Shell
  #check for the file in jump hose
  ls /tmp
  #copy the file to the app server1
  sudo scp -r /tmp/nautilus.txt.gpg tony@stapp01:/home/webapp
  #verify
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  ls /home/webapp
  ```