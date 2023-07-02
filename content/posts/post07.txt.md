---
author:
  name: "chxmxii_"
date: 2023-06-22
tags:
  - DevOps
  - Sysadmin
type:
- post
- posts
title: KodeKloud Engineer System Administration Challenges - P2
weight: 10
series:
- Hugo 101
---
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
---
## Linux Find Command

+ During a routine security audit, the team identified an issue on the Nautilus App Server. Some malicious content was identified within the website code. After digging into the issue they found that there 
might be more infected files. Before doing a cleanup they would like to find all similar files and copy them to a safe location for further investigation. Accomplish the task as per the following requirements:
+ a. On App Server 1 at location /var/www/html/official find out all files (not directories) having .js extension.
+ b. Copy all those files along with their parent directory structure to location /official on same server.
+ c. Please make sure not to copy the entire /var/www/html/official directory content.

###### Solution:

+ ```Shell
  #connect to app server 1
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #check if /official exists
  ls /official
  ls /var/www/html/
  #find all the js files under /var/www/html/official
  find /var/www/html/official -name "*.js" -type f -exec cp --parents {} /official/ \; 
  #verify
  ls /official/
  ```
---
## Linux Postfix Mail

+ xFusionCorp Industries has planned to set up a common email server in Stork DC. After several meetings and recommendations they have decided to use postfix as their mail transfer agent and dovecot as an IMAP/POP3 server. We would like you to perform the following steps:
+ Install and configure postfix on Stork DC mail server.
+ Create an email account javed@stratos.xfusioncorp.com identified by BruCStnMT5.
+ Set its mail directory to /home/javed/Maildir.
+ Install and configure dovecot on the same server.

###### Solution:

+ ```shell
  ## ssh to the mail server
  sshpass -p Gr00T123 ssh -o StrictHostKeyChecking groot@stmail01
  #switch to the root user
  sudo su -
  #Confirm that yum is installed
  rpm -qa | grep postfix
  #Install postfix on the mail server
  yum install postfix -y
  #Configure postfix on the server
  vi /etc/postfix/main.cf
  #Find the line with #myhostname & #mydomain and set it as follows
  >myhostname = stmail01.stratos.xfusioncorp.com
  >mydomain = stratos.xfusioncorp.com
  #Uncomment the '#myorigin=$mydomain' line
  >myorigin = $mydomain
  #Uncomment the '#inet_interfaces = all' line
  > inet_interfaces = all
  #Uncomment the '#mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain' line
  > mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
  #Uncomment the '#mynetworks = host IP address, => localhost' line and replace it accordingly
  >mynetworks = {host IP address}/24, 127.0.0.0/8
  #Uncomment the '#home_mailbox = Maildir/' line
  > home_mailbox = Maildir/
  #Save and quit the configuration file
  #Start postfix and confirm it is working
  systemctl start postfix
  systemctl status postfix
  #Create the user account for javed
  useradd javed
  passwd javed
  telnet stmail01 25
  #Enter the following settings:
  => EHLO localhost
  => mail from: javed@stratos.xfusioncorp.com
  => rcpt to: javed@stratos.xfusioncorp.com
  => DATA
  => test mail
  => quit
  #Install dovecot on mail server
  yum install dovecot -y
  #Configure dovecot
  vi /etc/dovecot/dovecot.conf
  #tip>:set nu
  #Uncomment '#protocols = imap pop3 lmtp'
  > save and quit :wq
  #modify 10-mail.conf
  vi /etc/dovecot/conf.d/10-mail.conf
  #Uncomment the line '#mail_location = maildir:~/Maildir'
  > save and quit :wq
  #modify 10-auth.conf
  vi /etc/dovecot/conf.d/10-auth.conf
  #Uncomment the line '#disable_plaintext_auth = yes'
  > disable_plaintext_auth = yes
  #Set the 'auth_mechanisms' line
  > auth_mechanisms = plain login
  > save and quit :wq
  #modfiy master.conf
  vi 10-master.conf
  #Uncomment and set the line '#user = '
  > user = postfix
  > group = postfix
  #Start dovecot service
  systemctl start dovecot
  #Test the configuration
  telnet stmail01 110
  > user javed
  > pass {given-password}
  > retr 1
  > quit
  > ss -tulnp
  ```

---

## Linux Process Troubleshooting

+ The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in Stratos DC.
+ Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not hosted any code yet on these servers so you need not to worry about if Apache isn't serving any pages or not, just make sure service is up and running. Also, do not try to change the Apache port on any host.

###### Solution:

+ ```Shell 
  #First, we need to identify the faulty app. lets use curl from the jump host
  curl http://stapp01:6200
  curl http://stapp02:6200
  curl http://stapp03:6200
  #In my case, stapp01 was the faulty app, lets ssh to it and find out whats the problem
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  systemctl start httpd
  systemctl status httpd
  httpd -t
  journalctl -xe
  cat /etc/httpd/conf/httpd_conf | grep Listen
  #lets install netstat
  yum install netstat -y
  netstat -tulnp |grep 6200
  #get the id of the current proccess using port 6200 and kill it, in my case it was 525
  kill -9 525
  #start the service and verify
  systemctl start httpd
  systemctl status httpd
  curl http://stapp01:6200
  ```
---
## Install and Configure PostgreSQL	

+ The Nautilus application development team has shared that they are planning to deploy one newly developed application on Nautilus infra in Stratos DC. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below:
+ a. Install and configure PostgreSQL database on Nautilus database server.
+ b. Create a database user kodekloud_sam and set its password to B4zNgHA7Ya.
+ c. Create a database kodekloud_db8 and grant full permissions to user kodekloud_sam on this database.
+ d. Make appropriate settings to allow all local clients (local socket connections) to connect to the kodekloud_db8 database through kodekloud_sam user using md5 method (Please do not try to encrypt password with md5sum).
+ e. At the end its good to test the db connection using these new credentials from root user or server's sudo use

###### Solution:
+ ```shell
  #ssh to the database server
  sshpass -p Sp\!dy ssh -o StrictHostKeyChecking=no peter@stdb01
  #switch to root user
  sudo su -
  #install postgresql
  yum search all postgresql
  yum whatprovides postgresql
  yum install postgresql-server postgresql-contrib
  #iniate the db
  postgresql-setup initdb
  #enable and start postgresql
  systemctl enable postgresql && systemctl start postgresql
  #create user and grant full permissions
  sudo -u postgres psql
  > CREATE USER <username> WITH PASSWORD <password>;
  > CREATE DATABASE <database_name>;
  > GRANT ALL PRIVILEGES ON DATABASE <databse_name> TO <username>; 
  >\q
  #configure to postgres to allow local socket cnx using md5 method
  vi /var/lib/psql/data/pg_hba.conf
  #edit the following lines
  >local all all md5
  >host all 127.0.0.1/32 md5
  #open postgresql.conf and uncomment listen_addresses line
  vi /var/lib/psql/data/postgresql.conf
  >listen_addresses = 'localhost'
  #restart psq service
  systemctl restart postgresql
  systemctl status postgresql
  #verifying
  psql -U <username> -d <database_name> -h 127.0.0.1 -W
  psql -U <username> -d <database_name> -h localhost -W
  ```
---
## Tomcat

+ The Nautilus application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting, they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:
+ a. Install tomcat server on App Server 2 using yum.
+ b. Configure it to run on port 6300.
+ c. There is a ROOT.war file on Jump host at location /tmp. Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e without specifying any sub-directory anything like this http://URL/ROOT .

###### Solution: 
+ ```Shell
  #ssh to app server 2
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02
  #switch to the root user
  sudo su -
  #install tomcat
  yum install tomcat -y
  #run tomcat on port 6300
  vi /etc/tomcat/server.xml
  > look for Port and change it to <Connector Port = "6300">
  systemctl start tomcat
  systemctl status tomcat
  #from the jumphost copy the ROOT.war to app server2
  scp ROOT.war steve@lstapp02:/tmp/
  #go back to the app server2
  mv /tmp/ROOT.war /var/lib/tomcat/webapps/
  #verify
  curl localhost:6300
  ```
---
## Install and configure SFTP

+ Some of the developers from Nautilus project team have asked for SFTP access to at least one of the app server in Stratos DC. After going through the requirements, the system admins team has decided to configure the SFTP server on App Server 2 server in Stratos Datacenter. Please configure it as per the following instructions:
+ a. Create an SFTP user mariyam and set its password to BruCStnMT5.
+ b. Password authentication should be enabled for this user.
+ c. Set its ChrootDirectory to /var/www/appdata.
+ d. SFTP user should only be allowed to make SFTP connections.
  
###### Solution:
+ ```Shell
  #ssh to app server2
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02  
  #switch to the root user
  sudo su -
  #make the folder appdata
  mkdir -p /var/www/appdata
  #change perms and the owner of appdata folder
  chmod 755 /var/www/appdata/
  chown root:root /var/www/appdata/
  #add user mariyam with appdata as its chrootDir
  useradd -d /var/www/appdata/ mariyam
  #verify
  cat /etc/passwd
  #configure sshd
  vi /etc/ssh/sshd_config
  >PasswordAuthentication yes
	>ChrootDirectory %h
	>AllowTCPForwarding no
	>X11Forwarding no
	>ForceCommand internal-sftp
	>AllowAgentForwarding no
	>PermitTunnel no 
  #restart sshd 
  systemctl restart sshd
  #verify
  sftp mariyam@stapp02
  ```
 ---
 ## IPtables Installation And Configuration

+ We have one of our website up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 3000 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with below given requirements:
+ Install iptables and all its dependencies on each app host.
+ Block incoming port 3000 on all apps for everyone except for LBR host.
+ Make sure the rules should persist even after system reboot.

###### Solution:
+ ```shell
  #ssh to the app servers
  sshpass -p <password> ssh -o StrictHostKeyChecking=no <user>@<host>
  #switch to the root user
  sudo su -
  #install iptables
  yum install iptables-services -y
  iptables --help
  #start the iptables 
  systemctl start iptables
  systemctl enable iptables
  systemctl status iptables
  iptables --list
  #allow LBR host
  iptables -R INPUT 5 -p tcp --dport 3000 -s 172.16.238.14 -j ACCEPT #-s is for source ip of the LBR, dport is destination port.
  #block the incoming reqs
  iptables -A INPUT -p tcp --dport 3000 -j DROP
  #save
  service iptables save
  #verify from the jump host
  curl 172.16.238.10:3000
  #ssh to the LBR host
  sshpass -p Mischi3f ssh -o StrictHostKeyChecking=no loki@stlb01
  #verify
  curl 172.16.238.10:3000
  ```