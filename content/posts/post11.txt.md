---
author:
  name: "chxmxii_"
date: 2023-08-20
tags:
  - DevOps
  - Jenkins
  - Sysadmin
type:
- post
- posts
title: KKE - Jenkins Challenges
weight: 10
series:
- Hugo 101
---
![](/files/jenkins.png#center)

---
## Jenkins Installation

+ The DevOps team of xFusionCorp Industries is planning to setup some CI/CD pipelines. After several meetings they have decided to use Jenkins server. So, we need to setup a Jenkins Server as soon as possible. Please complete the task as per requirements mentioned below:
+ 1. Install jenkins on jenkins server using yum utility only, and start its service. You might face timeout issue while starting the Jenkins service, please refer this link for help.
+ 2. Jenkin's admin user name should be theadmin, password should be Adm!n321, full name should be Rose and email should be rose@jenkins.stratos.xfusioncorp.com.
Note:
+ 1. For this task, ssh into the jenkins server using user root and password S3curePass from jump host.
+ 2. After installing the Jenkins server, please click on the Jenkins button on the top bar to access Jenkins UI and follow the on-screen instructions to create an admin user.
  
###### Solution
+ ``` shell
  sshpass -p S3curePass ssh -o StrictHostKeyChecking=no root@jenkins
  yum install -y wget
  wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
  rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
  yum install -y java-17-openjdk
  yum install -y jenkins
  systemctl daemon-reload
  systemctl start jenkins
  cat /var/lib/jenkins/secrets/initialAdminPassword
  #open jenkins and create your first admin user
  ```
![](/files/jenkinstask.png#center)

---
## Jenkins Plugins

+ The Nautilus DevOps team has recently setup a Jenkins server, which they want to use for some CI/CD jobs. Before that they want to install some plugins which will be used in most of the jobs. Please find below more details about the task
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and Adm!n321 password
+ Once logged in, install Git and GitLab plugins. You might need to restart Jenkins service to install these plugins, so we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre.
+ Note:
  + Once you restart Jenkins service it will go down for some time so please wait for the Jenkins login page to come back before clicking on the Check button.
  + For these scenarios requiring changes to be done in a web UI, please take screenshots so you can share them with us for review in case your task is marked incomplete. You may also consider using screen recording software like loom.com to record and share your work.
  
###### Solution
+ ``` M
  Dashboard > Manage Jenkins > Plugin Manager.
  Search for both plugins Git,Gitlab.
  Click on "Restart Jenkins when installation is complete and no jobs".
  ```
---
## Jenkins Create Users

+ The Nautilus team is planning to use Jenkins for some of their CI/CD pipelines. DevOps team has just installed a fresh Jenkins server and they are configuring it further to be available for use.
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.
+ It has only a sample job for now. A new developer has joined the Nautilus application development team and they want this user to be added to the Jenkins server as per the details mentioned below:
+ Create a jenkins user named mark with LQfKeWWxWD password, their full name should be Mark (it is case sensitive).
+ Using Project-based Matrix Authorization Strategy assign overall read permission to mark user.
+ Remember to remove all permissions for Anonymous users (if given) and make sure admin user has overall Administer permissions.
+ There is one existing job, make sure mark only has read permissions to that job (we are not worried about other permissions like Agent, SCM, etc.).
+ Note:
  + You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, in case Jenkins UI gets stuck when Jenkins service restarts in the back end, please make sure to refresh the UI page.
  + Do not immediately click on Finish button if you have restarted the Jenkins service, please wait for Jenkins login page to come back before finishing your task.
  + For these kind of scenarios that required changes to be done from a web UI, please take screenshots of your work so that you can share the same with us for review purpose (in case your task is marked incomplete or failed). You may also consider using a screen recording software such as loom.com to record and share your work.

###### Solution
+ ``` M
  Dashboard > Manage Jenkins > Jenkins’ own user database > Create User. # Add new user and create user "mark".
  Go to "Plugin Manager" and install "Project-based Matrix Authorization Strategy".
  Change the "Authorization" strategy to "Project-based Matrix"
  add "admin" and assign the "OVERALL ADMINISTER".
  add "mark" and assign both "OVERALL READ" and "JOB READ".
  save the changes, and restart jenkins.
  ```
---
## Jenkins Folders

+ The DevOps team of xFusionCorp Industries is planning to create a number of Jenkins jobs for different tasks. To easily manage the jobs within Jenkins UI they decided to create different Folders for all Jenkins jobs based on usage/nature of these jobs. Based on the requirements shared below please perform the below mentioned task:
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.
+ There are already two jobs httpd-php and services.
+ Create a new folder called Apache under Jenkins UI.
+ Move the above mentioned two jobs under Apache folder.
+ Note:
  + You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
  + For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

###### Solution
+ ```M
  Install a plugin called "Folder" by cloudBee
  Create a new Item called "Apache" and set the type of the item to folder
  Right click on the jobs and move them to the apache folder
  Restart Jenkins.
  ```
---
## Jenkins Install Packages

+ Some new requirements have come up to install and configure some packages on the Nautilus infrastructure under Stratos Datacenter. The Nautilus DevOps team installed and configured a new Jenkins server so they wanted to create a Jenkins job to automate this task. Find below more details and complete the task accordingly:
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and Adm!n321 password.
+ Create a Jenkins job named install-packages and configure it to accomplish below given tasks.
+ Add a string parameter named PACKAGE.
+ Configure it to install a package on the storage server in Stratos Datacenter provided to the $PACKAGE parameter.
+ Note:
  + You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also some times Jenkins UI gets stuck when Jenkins service restarts in the back end so in such case please make sure to refresh the UI page.
  + Make sure Jenkins job passes even on repetitive runs as validation may try to build the job multiple times.
  + For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.
  
###### Solution
+ ```M
  Install The required SSH Plugins (SSH server, SSH plugin, SSH Credentials Plugin, SSH Agent Plugin)
  Create a new credential and store the username:password of user natasha
  Go the main config and add new SSH site add the hostname of storage server (ststr01) and add the credential saved by user natasha
  Create a new job called "install-packages" and add a string parameter PACKAGE.
  Click on "execute shell script on remote hosting using ssh" {BUILD}, add the following command
  `echo Bl@kW | sudo -S yum install -y $PACKAGE`
  You can test the job, trying to give PACKAGE a value of "wget, zip" etc..

  ```