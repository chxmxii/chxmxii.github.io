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
## Git Fork a Repository

+ There is a Git server used by the Nautilus project teams. Recently a new developer Jon joined the team and needs to start working on a project. To do so, he needs to fork an existing Git repository. Below you can find more details:
+ Click on the Gitea UI button on the top bar. You should be able to access the Gitea page.
+ Login to Gitea server using username jon and password Jon_pass123.
+ There you will see a Git repository sarah/story-blog, fork it under jon user.
+ Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

###### Solution:
+ ```M
  Login
  Go to the repo sarah/story-blog and fork it.
  ```
---
## Git Repository Update

+ The Nautilus development team started with new project development. They have created different Git repositories to manage respective project's source code. One of the repositories /opt/cluster.git was created recently. The team has given us a sample index.html file that is currently present on jump host under /tmp directory. The repository has been cloned at /usr/src/kodekloudrepos on storage server in Stratos DC.
+ Copy sample index.html file from jump host to storage server under cloned repository at /usr/src/kodekloudrepos/cluster, further add/commit the file and push to the master branch.

###### Solution:
+ ```shell
  $ scp /tmp/index.html natasha@ststor01:/tmp/
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cp /tmp/index.html /usr/src/kodekloudrepos/cluster/
  $ sudo git add *
  $ sudo git commit -m "kodekloud"
  $ sudo git push
  $ sudo git logs
  ```

---
## Git Repository Update

+ Nautilus developers are actively working on one of the project repositories, /usr/src/kodekloudrepos/blog. They were doing some testing and created few test branches, now they want to clean those test branches. Below are the requirements that have been shared with the DevOps team:
+ On Storage server in Stratos DC delete a branch named xfusioncorp_blog from /usr/src/kodekloudrepos/blog git repo.
###### Solution:
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/blog 
  $ sudo git branch -a
  $ sudo git checkout master #to switch the current branch to master
  $ sudo git branch -d xfusioncorp_blog
  $ sudo git bracnh -a
  ```
