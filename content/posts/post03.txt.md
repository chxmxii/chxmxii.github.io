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
--- 

## Linux Postfix Troubleshooting	

+ Some users of the monitoring app have reported issues with xFusionCorp Industries mail server. They have a mail server in Stork DC where they are using postfix mail transfer agent. Postfix service seems to fail. Try to identify the root cause and fix it.

###### Solution:
+ ``` Shell
  #connect to the mail server
  sshpass -p Gr00T123 ssh -o StrictHostKeyChecking=no groot@stmail01
  #switch to the root user
  sudo su -
  #start the postfix service
  systemctl start postfix 
  #notice the error we get, lets dig into it and understand the root problem
  systemctl status postifx -l
  journalctl -xe 
  #According to the logs, the error in Postfix occurred because the "inet_interfaces" parameter was set to both "all" and "localhost," causing a conflict. This conflicting configuration resulted in a malfunction of the system.
  #We can fix this by commenting "inet_interfaces=localhost" in /etc/postfix/main.cf
  vi /etc/postfix/main.cf > add "#" to "inet_interfaces=localhost"
  #start the service
  systemctl start postfix
  systemctl status postfix
  #verify
  telnet stmail01 25
  ```
---
## Linux Services	

+ As per details shared by the development team, the new application release has some dependencies on the back end. There are some packages/services that need to be installed on all app servers under Stratos Datacenter. As per requirements please perform the following steps:
+ a. Install nscd package on all the application servers.
+ b. Once installed, make sure it is enabled to start during boot.

###### Solution:
+ ```Shell
  #ssh to the app servers one by one
  sshpass -p <password> ssh -o StrictHostKeyChecking=no <name>@stapp0{1,2,3}
  #switch to root user
  sudo su -
  #search for the nscd package
  yum search nscd
  #install the package
  yum install nscd -y
  #start and enbale the service
  systemctl status nscd
  systemctl enable --now nscd
  #verify the service status
  systemctl status nscd
  ```
---
## Linux User Expiry	
+ A developer kirsty has been assigned Nautilus project temporarily as a backup resource. As a temporary resource for this project, we need a temporary user for kirsty. It’s a good idea to create a user with a set expiration date so that the user won't be able to access servers beyond that point.
+ Therefore, create a user named kirsty on the App Server 1. Set expiry date to 2021-02-17 in Stratos Datacenter. Make sure the user is created as per standard and is in lowercase.

###### Solution:
+ ```Shell 
  #Connect to app server 1 
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #Switch to root user
  sudo su -
  #create new user with expiry date
  useradd -e 2021-02-17 kirsty
  #verify
  chage -l kirsty
  ```
---
## Disable Root Login
+ After doing some security audits of servers, xFusionCorp Industries security team has implemented some new security policies. One of them is to disable direct root login through SSH.
+ Disable direct SSH root login on all app servers in Stratos Datacenter.

###### Solution:
+ ```Shell
  #ssh to the app servers
  sshpass -p <password> ssh -o StrictHostKeyChecking=no <user>@<host>
  #switch to the root user
  sudo su -
  #edit the sshd_config file
  vi /etc/ssh/sshd_config
  add "PermitRootLogin no" and save the file and quit
  #restart ssh daemon 
  systemctl restart sshd
  systemctl status sshd
  ```
---
## Linux Banner	

+ During the monthly compliance meeting, it was pointed out that several servers in the Stratos DC do not have a valid banner. The security team has provided serveral approved templates which should be applied to the servers to maintain compliance. These will be displayed to the user upon a successful login.
+ Update the message of the day on all application and db servers for Nautilus. Make use of the approved template located at /home/thor/nautilus_banner on jump host

###### Solution
+ ```Shell
  #scp is not installed in the db server, lets first install it first.
  sshpass -p Sp\!dy ssh -o StrictHostKeyChecking=no peter@stdb01
  #switch to the root user and install the required package
  sudo su -
  yum whatprovides scp
  yum install openssh-clients -y
  #now CTRL^D twice and go back to the jump host to copy the template.
  scp nautilus_banner peter@stdb01:/tmp/motd
  scp nautilus_banner tony@stapp01:/tmp/motd
  scp nautilus_banner steve@stapp02:/tmp/motd
  scp nautilus_banner banner@stapp03:/tmp/motd
  #ssh again to the server
  sshpass -p Sp\!dy ssh -o StrictHostKeyChecking=no peter@stdb01
  #move the /tmp/motd file to /etc/motd
  sudo mv /tmp/motd /etc/motd
  #verify
  cat /etc/motd
  #CTRL^D twice and reconnect to the server via ssh to see the changes.
  sshpass -p Sp\!dy ssh -o StrictHostKeyChecking=no peter@stdb01
  #Redo the same process on all app servers.
  ```
---
## Web Server Security	

+ During a recent security audit, the application security team of xFusionCorp Industries found security issues with the Apache web server on Nautilus App Server 1 server in Stratos DC. They have listed several security issues that need to be fixed on this server. Please apply the security settings below:
+ a. On Nautilus App Server 1 it was identified that the Apache web server is exposing the version number. Ensure this server has the appropriate settings to hide the version number of the Apache web server.
+ b. There is a website hosted under /var/www/html/blog on App Server 1. It was detected that the directory /blog lists all of its contents while browsing the URL. Disable the directory browser listing in Apache config.
+ c. Also make sure to restart the Apache service after making the changes.

###### Solution
+ ```Shell
  #ssh to the app server 1
  sshpass -p Ir0nM@n -o StrictHostKeyChecking=no tony@stapp01
  #check the httpd status
  sudo systemctl status httpd
  #open the httpd.conf file with vi
  sudo vi /etc/httpd/conf/httpd.conf
  #a. to disable the version discolusre you can append the following two lines to the httpd.conf
  .> ServerTokens Prod
  .> ServerSignature Off
  #b. to disable the directory browser listing, make sure to remove Indexes from options
  Before > Options Indexes FollowSymLinks
  After > Options FollowSymLinks
  #save and quit
  #c. restart the server and curl to verify
  sudo systemctl start httpd && sudo systemctl status httpd
  curl -I stapp01:8080/
  #TIP: you can press "/" and write "Options" in vi to search for that line. press n to move to the next matched item.
  #ref: https://www.tecmint.com/hide-apache-web-server-version-information/
  #ref: https://stackoverflow.com/questions/2530372/how-do-i-disable-directory-browsing
  ```
---
## Configure Local Yum repos	

+ The Nautilus production support team and security team had a meeting last month in which they decided to use local yum repositories for maintaing packages needed for their servers. For now they have decided to configure a local yum repo on Nautilus Backup Server. This is one of the pending items from last month, so please configure a local yum repository on Nautilus Backup Server as per details given below.
+ a. We have some packages already present at location /packages/downloaded_rpms/ on Nautilus Backup Server.
+ b. Create a yum repo named epel_local and make sure to set Repository ID to epel_local. Configure it to use package's location /packages/downloaded_rpms/.
+ c. Install package vim-enhanced from this newly created repo.
  
###### Solution:
+ ```Shell 
  #Connect to the backup server
  sshpass -p H@wk3y3	ssh -o StrictHostKeyChecking=no clint@stbkp01
  #Switch to the root user
  sudo su -
  #there is actually 2 ways to create yum repos, either you do it manually or you can do it with "yum-config-manager --add-repo=<url>"
  cat <<\ EOF > /etc/yum_repos.d/epel_local.repo
  [epel_local]
  name=epel_local
  baseurl=file:///packages/downloaded_rpms/
  enabled=1
  gpgcheck=0
  EOF
  #clean and list the repos
  yum clean all
  yum repo list
  #install the vim-enhanced
  yum install vim-enhanced -y
  ```
---
## Setup SSL for Nginx	

+ The system admins team of xFusionCorp Industries needs to deploy a new application on App Server 1 in Stratos Datacenter. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:
+ Install and configure nginx on App Server 1.
+ On App Server 1 there is a self signed SSL certificate and key present at location /tmp/nautilus.crt and /tmp/nautilus.key. Move them to some appropriate location and deploy the same in Nginx.
+ Create an index.html file with content Welcome! under Nginx document root.
+ For final testing try to access the App Server 2 link (either hostname or IP) from jump host using curl command. For example curl -Ik https://<app-server-ip>/.
  
###### Solution

+ ```Shell
  #ssh to the app server 1
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #Switch to root user
  sudo su -
  #search for the nginx package
  yum whatprovides nginx
  yum search nginx
  #install epel-release & nginx
  yum install epel-release -y
  yum install nginx
  #copy the self-signed cert
  cp /tmp/nautilus.crt /etc/pki/CA/certs/ && cp /tmp/nautilus.key /etc/pki/CA/private/
  ```
  edit the /etc/nginx/nginx.conf to look like this
+ ``` Shell
  #For more information on configuration, see:
  #* Official English Documentation: http://nginx.org/en/docs/
  #* Official Russian Documentation: http://nginx.org/ru/docs/
  user nginx;
  worker_processes auto;
  error_log /var/log/nginx/error.log;
  pid /run/nginx.pid;

  #Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
  include /usr/share/nginx/modules/*.conf;

  events {
      worker_connections 1024;
  }

  http {
      log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';

      access_log  /var/log/nginx/access.log  main;

      sendfile            on;
      tcp_nopush          on;
      tcp_nodelay         on;
      keepalive_timeout   65;
      types_hash_max_size 4096;

      include             /etc/nginx/mime.types;
      default_type        application/octet-stream;

      # Load modular configuration files from the /etc/nginx/conf.d directory.
      # See http://nginx.org/en/docs/ngx_core_module.html#include
      # for more information.
      include /etc/nginx/conf.d/*.conf;

      server {
          listen       80;
          listen       [::]:80;
          server_name  <your_server_ip_here>;
          root         /usr/share/nginx/html;

          # Load configuration files for the default server block.
          include /etc/nginx/default.d/*.conf;

          error_page 404 /404.html;
          location = /404.html {
          }

          error_page 500 502 503 504 /50x.html;
          location = /50x.html {
          }
      }

  #Settings for a TLS enabled server.
      server {
          listen       443 ssl http2;
          listen       [::]:443 ssl http2;
          server_name  <your_server_ip_from_here>;
          root         /usr/share/nginx/html;

          ssl_certificate "/etc/pki/CA/certs/nautilus.crt";
          ssl_certificate_key "/etc/pki/CA/private/nautilus.key";
          ssl_session_cache shared:SSL:1m;
          ssl_session_timeout  10m;
          ssl_ciphers HIGH:!aNULL:!MD5;
          ssl_prefer_server_ciphers on;

          # Load configuration files for the default server block.
          include /etc/nginx/default.d/*.conf;

          error_page 404 /404.html;
              location = /40x.html {
          }

          error_page 500 502 503 504 /50x.html;
              location = /50x.html {
          }
      }
    ```
  Now lets add index.html file and start the nginx service
+ ```Shell
  #Now, you will have to remove the index.html under the nginx root because is linked to another file.
  rm -rf /usr/share/nginx/html/index.html
  #create new index.html under nginx root contains "welcome!"
  echo "Welcome!" > /usr/share/nginx/html/index.html
  #start nginx 
  systemctl start nginx
  systemctl status
  #verify
  curl localhot
  #going back to the jump host lets curl
  curl -Ik https://172.16.238.10	
  curl -k https://172.16.238.10
  ```
  ---
## Application Security

+ We have a backup management application UI hosted on Nautilus backup server in Stratos DC. That backup management application code is deployed under Apache on the backup server itself, and Nginx is running as a reverse proxy on the same server. Apache and Nginx ports are 6100 and 8093, respectively. We have iptables firewall installed on this server. Make the appropriate changes to fulfill the requirements mentioned below:
+ a. We want to open all incoming connections to Nginx's port and block all incoming connections to Apache's port. Also make sure rules are permanent.

###### Solution:
+ ```Shell
  #ssh to the backup server
  sshpass -p H@wk3y3 ssh -o StrictHostKeyChecking=no clint@stbkp01
  #switch to root user
  sudo su -
  #verify the ports of nginx and apache
  ss -lntp | grep nginx
  ss -lntp | grep http
  #configure the firewall to match the desired behavior
  iptables -A INPUT -p tcp --dport 8093 -m conntrack --cstate NEW,ESTABLISHED -j ACCEPT
  iptables -A INPUT -p tcp --dport 6100 -m conntrack --cstate NEW -j REJECT
  #save the rules to remain permanent
  sudo iptables-save > /etc/sysconfig/iptables
  service iptables save
  #veriffy
  cat /etc/sysconfig/iptables
  iptables -L -n -v
  #verify from jump host
  telnet stkbp01 8093
  telnet stkbp01 6100
  ```
---
  ## Bash Script 

+ The production support team of xFusionCorp Industries is working on developing some bash scripts to automate different day to day tasks. One is to create a bash script for taking websites backup. They have a static website running on App Server 1 in Stratos Datacenter, and they need to create a bash script named official_backup.sh which should accomplish the following tasks. (Also remember to place the script under /scripts directory on App Server 1)
+ a. Create a zip archive named xfusioncorp_official.zip of /var/www/html/official directory.
+ b. Save the archive in /backup/ on App Server 1. This is a temporary storage, as backups from this location will be clean on weekly basis. Therefore, we also need to save this backup archive on Nautilus Backup Server.
+ c. Copy the created archive to Nautilus Backup Server server in /backup/ location.
+ d. Please make sure script won't ask for password while copying the archive file. Additionally, the respective server user (for example, tony in case of App Server 1) must be able to run it.

###### Solution:
+ ```Shell
  #ssh to app server 1
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #cd to /scripts folder and create a official_backup.sh file
  cd /scripts
  vi official_backup.sh
  ```
  After opening the officila_backup.sh file, copy the following script, save and quit.
+ ```Shell
  #!/bin/bash
  #make an archive for the official folder under /var/www/html/ "-r for recurisve" 
  zip -r /backup/xfusioncorp_official.zip /var/www/html/official
  #copy the archive file to the backup server
  scp /backup/xfusioncorp_official.zip clint@stbkp01:/backup/
  ```
  now, to make sure the script won't ask for password while copying the archive file, lets generate a ssh key
+ ```shell
  #generate an ssh key
  ssh-keygen
  #copy the generated key to the backup server
  ssh-copy-id clint@stbkp01
  #ssh to verify
  ssh clint@stkp01
  #add exec perm to the official_backup.sh file
  chmod +x official_backup.sh
  #run the script
  ./official_backup
  #ssh to backserver and verify
  ssh clint@stkp01
  ls /backup/
  ``` 
---
## Linux Configure sudo

+ We have some users on all app servers in Stratos Datacenter. Some of them have been assigned some new roles and responsibilities, therefore their users need to be upgraded with sudo access so that they can perform admin level tasks.
+ a. Provide sudo access to user james on all app servers.
+ b. Make sure you have set up password-less sudo for the user.
  
###### Solution :
+ ```Shell
  #ssh to all app servers one by one
  ssh -p <password> ssh -o StrictHostKeyChecking=no <name>@<hostname>
  #switch to root user
  sudo su -
  #Provide sudo access to user james
  echo "james ALL=(ALL) NOPASSWD:ALL" > /etc/sudoer.d/james
  #verify
  su james
  sudo id
  sudo su
  ```
---
## Apache Redirects	

+ The Nautilus devops team got some requirements related to some Apache config changes. They need to setup some redirects for some URLs. There might be some more changes need to be done. Below you can find more details regarding that:
+ httpd is already installed on app server 3. Configure Apache to listen on port 5003.
+ Configure Apache to add some redirects as mentioned below:
  a.) Redirect http://stapp03.stratos.xfusioncorp.com:<Port>/ to http://www.stapp03.stratos.xfusioncorp.com:<Port>/ i.e non www to www. This must be a permanent redirect i.e 301
  b.) Redirect http://www.stapp03.stratos.xfusioncorp.com:<Port>/blog/ to http://www.stapp03.stratos.xfusioncorp.com:<Port>/news/. This must be a temporary redirect i.e 302.

###### Solution :
+ ```Shell
  #ssh to app server 3
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  #Switch to the root user
  sudo su -
  #verify httpd is alr installed
  rpm -qa | grep httpd
  #Configure apache to listen on port 5003
  vi /etc/httpd/conf/httpd.conf
  Look for the line that starts with Listen and change it to > Listen 5003
  ```
  Configure redirection
+ ```Shell
  vi /etc/httpd/conf.d/main.conf
  #copy the following lines
  <VirtualHost *:5003>
  ServerName http://stapp03.stratos.xfusioncorp.com:5003/
  Redirect 301 /  http://www.stapp03.stratos.xfusioncorp.com:5003/
  </VirtualHost>

  <VirtualHost *:5003>
  ServerName http://www.stapp03.stratos.xfusioncorp.com:5003/blog/
  Redirect 302 / http://www.stapp03.stratos.xfusioncorp.com:5003/news/
  </VirtualHost>
  ```
+ ```Shell
  #Restart htttpd
  systemctl restartd httpd
  #Verify 
  curl http://stapp03.stratos.xfusioncorp.com:5003/
  curl http://www.stapp03.stratos.xfusioncorp.com:5003/
  curl http://www.stapp03.stratos.xfusioncorp.com:5003/blog/
  curl http://www.stapp03.stratos.xfusioncorp.com:5003/news
  ```
---
## Linux GPG Encryption	

+ We have confidential data that needs to be transferred to a remote location, so we need to encrypt that data.We also need to decrypt data we received from a remote location in order to understand its content.
+ On storage server in Stratos Datacenter we have private and public keys stored /home/*_key.asc. Use those keys to perform the following actions.
+ Encrypt /home/encrypt_me.txt to /home/encrypted_me.asc.
+ Decrypt /home/decrypt_me.asc to /home/decrypted_me.txt. (Passphrase for decryption and encryption is kodekloud).

###### Solution

+ ```Shell
  #ssh to the storage server
  sshpass -p Bl@ckW ssh -o StrictHostKeyChecking=no natasha@ststor01
  #swith to the root user
  sudo su -
  #check for the keys
  ls /home/
  cat /home/encrypt_me.txt
  cat /home/decrypt_me
  cd /home
  #Import the keys 
  gpg --import public_key.asc
  gpg --import private_key.asc
  #verify
  gpg --list-keys
  gpg --list-secret-keys
  #encryp the encrypt_me.txt file
  gpg --encrypt -r kodekloud@kodekloud.com --armor < encrypt_me.txt -o encrypted_me.asc
  #decrypt the message, you will be prompted with a passphrase which is "kodekloud"
  gpg --decrypt decrypt_me.asc > decrypted_me.txt
  #validate
  cat decrypted_me.txt
  cat decrypt_me.asc
  cat encrypt_me.txt
  cat encrpyed_me.asc
  ```
---
## Linux LogRotate

+ The Nautilus DevOps team is ready to launch a new application, which they will deploy on app servers in Stratos Datacenter. They are expecting significant traffic/usage of httpd on app servers after that. This will generate massive logs, creating huge log files. To utilise the storage efficiently, they need to compress the log files and need to rotate old logs. Check the requirements shared below:
+ a. In all app servers install httpd package.
+ b. Using logrotate configure httpd logs rotation to monthly and keep only 3 rotated logs.
(If by default log rotation is set, then please update configuration as needed)

###### Solution :
+ ```Shell
  #ssh to the app server
  sshpass -p <passwd> ssh -o StrictHostKeyChecking=no <user>@<hostname>
  #switch to root user
  sudo su -
  #Install httpd package
  yum install httpd -y
  #configure logrotate to rotate httpd logs
  vi /etc/logrotate.d/http
  #start httpd service
  systemctl start httpd
  systemctl status httpd
  ```
  copy this into the http file under logrotate.d/
  ```Shell
  /var/log/httpd/*log {
    monthly
    missingok
    rotate 3
    notifempty
    sharedscripts
    compress
    postrotate
        /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
    endscript
  }
  ```
---
## Apache heading

+ We are working on hardening Apache web server on all app servers. As a part of this process we want to add some of the Apache response headers for security purpose. We are testing the settings one by one on all app servers. As per details mentioned below enable these headers for Apache:
+ Install httpd package on App Server 3 using yum and configure it to run on 3003 port, make sure to start its service.
+ Create an index.html file under Apache's default document root i.e /var/www/html and add below given content in it.
+ Welcome to the xFusionCorp Industries!
+ Configure Apache to enable below mentioned headers:
+ X-XSS-Protection header with value 1; mode=block
+ X-Frame-Options header with value SAMEORIGIN
+ X-Content-Type-Options header with value nosniff
=> Note: You can test using curl on the given app server as LBR URL will not work for this task

###### Solution:
+ ```Shell
  #ssh to server app 3
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no ssh banner@stapp03
  #switch to root user
  sudo su -
  #install httpd
  yum install httpd -y
  #configure httpd to listen on port 3003, and enable the mentioned headers
  vi /etc/httpd/conf/httpd.conf
  EDIT > Listen 3003
  INSERT > Header set X-XSS-Protection "1; mode=block"
  INSERT > Header set X-Content-Type-Options nosniff
  INSERT > Header always append X-Frame-Options SAMEORIGIN
  #create index.html
  echo "Welcome to the xFusionCorp Industries!" > /var/www/html/index.html
  #start the http service
  systemctl start httpd
  systemctl status httpd
  #verify
  curl -i localhost:3003
  #verify again from jumphost
  curl -i http://stapp03:3003
  ```
---
## Install package

+ As per new application requirements shared by the Nautilus project development team, serveral new packages need to be installed on all app servers in Stratos Datacenter. Most of them are completed except for git.
+ Therefore, install the git package on all app-servers.
  
###### Solution

+ ```Shell
  #ssh to app server 1,2 and 3
  sshpass -p <pass> ssh -o StrictHostKeyChecking=no <user>@<hostname>
  #switch to the root user
  sudo su -
  #list all the installed packages
  rpm -qa | grep git
  #install git
  yum install git -y
  #verify
  yum list installed | grep git
  git version
  ```