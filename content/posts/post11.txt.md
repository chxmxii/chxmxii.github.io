---
author:
  name: "chxmxii_"
date: 2023-08-28
tags:
  - DevOps
  - Jenkins
  - Sysadmin
type:
- post
- posts
title: KKE Jenkins Challenges
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
+ Dashboard > Manage Jenkins > Plugin Manager.
+ Search for both plugins Git,Gitlab.
+ Click on "Restart Jenkins when installation is complete and no jobs".