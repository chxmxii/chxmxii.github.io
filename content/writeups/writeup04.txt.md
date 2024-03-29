---
author:
  name: "chxmxii_"
date: 2023-05-20
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
---
## Jenkins Views

+ The DevOps team of xFusionCorp Industries is planning to create a number of Jenkins jobs for different tasks. So to easily manage the jobs within Jenkins UI they decided to create different views for all Jenkins jobs based on usage/nature of these jobs, - for example nautilus-crons view for all cron jobs. Based on the requirements shared below please perform the below mentioned task:
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.
+ Create a Jenkins job named nautilus-test-job.
+ Configure this job to run a simple bash command i.e echo "hello world!!".
+ Create a view named nautilus-crons (should be a List View) and make sure nautilus-test-job and nautilus-cron-job (which is already present on Jenkins) jobs are listed under this new view.
+ Schedule this newly created job to build periodically at every minute i.e * * * * * (please make sure to use the cron expression exactly same how it is mentioned here)
+ Make sure the job builds successfully.
+ Note:
+ You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.

+ ```M
  Login to jenkins
  Create a new Item (JOB) with name "nautilus-test-job"
  Within the "Build Trigger" select the option "Build periodically" and on the schedule box write "* * * * *"
  Add "Execute as a shell commmand" and put the command `echo 'hello world!!'`. SAVE!
  New create a new view in the dashboard and add the nautilus-test-job and nautilus-cron-job as a LIST VIEW.
  Make sure to RUN the jobs before submitting.
  ```
---
## Jenkins Parameterized Builds

+ A new DevOps Engineer has joined the team and he will be assigned some Jenkins related tasks. Before that, the team wanted to test a simple parameterized job to understand basic functionality of parameterized builds. He is given a simple parameterized job to build in Jenkins. Please find more details below:
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.
+ Create a parameterized job which should be named as parameterized-job
+ Add a string parameter named Stage; its default value should be Build.
+ Add a choice parameter named env; its choices should be Development, Staging and Production.
+ Configure job to execute a shell command, which should echo both parameter values (you are passing in the job).
+ Build the Jenkins job at least once with choice parameter value Development to make sure it passes.
+ Note:
+ You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.
  
###### Solution
+ ```M
  Login to jenkins with the user admin
  Create a new job, add two parametes String and choices 
  Add build "Execute as shell" and run the command "echo $Stage \n echo $env"
  Run the job.
  ```
---
## Jenkins Workspace

+ Some developers are working on a common repository where they are testing some features for an application. They are having three branches (excluding the master branch) in this repository where they are adding changes related to these different features. They want to test these changes on Stratos DC app servers so they need a Jenkins job using which they can deploy these different branches as per requirements. Configure a Jenkins job accordingly.
+ Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.
+ Similarly, click on Gitea button to access the Gitea page. Login to Gitea server using username sarah and password Sarah_pass123.
+ There is a Git repository named web_app on Gitea where developers are pushing their changes. It has three branches version1, version2 and version3 (excluding the master branch). You need not to make any changes in the repository.
+ Create a Jenkins job named app-job.
+ Configure this job to have a choice parameter named Branch with choices as given below:
  ```text
  version1
  version2
  version3
  ```
+ Configure the job to fetch changes from above mentioned Git repository and make sure it should fetches the changes from the respective branch which you are passing as a choice in the choice parameter while building the job. For example if you choose version1 then it must fetch and deploy the changes from branch version1.
+ Configure this job to use custom workspace rather than a default workspace and custom workspace directory should be created under /var/lib/jenkins (for example /var/lib/jenkins/version1) location rather than under any sub-directory etc. The job should use a workspace as per the value you will pass for Branch parameter while building the job. For example if you choose version1 while building the job then it should create a workspace directory called version1 and should fetch Git repository etc within that directory only.
+ Configure the job to deploy code (fetched from Git repository) on storage server (in Stratos DC) under /var/www/html directory. Since its a shared volume.

###### Solution
+ ```text
  login to jenkins
  Install the following plugins (git&ssh) 
  ```