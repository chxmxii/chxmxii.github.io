---
author:
  name: "chxmxii_"
date: 2023-08-15
tags:
  - DevOps
  - Sysadmin
  - Ansible
type:
- post
- posts
title: KKE - Git Challenges 
weight: 10
series:
- Hugo 101
---
![](/files/git.png#center)

---
## Git Install and Create Bare Repository

+ The Nautilus development team shared requirements with the DevOps team regarding new application development.—specifically, they want to set up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:
+ Install git package using yum on Storage server.
+ After that create a bare repository /opt/blog.git (make sure to use exact name).

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ sudo yum install -y git
  $ sudo mkdir /opt/blog.git && cd $_
  $ sudo git init -bare 
  ```
---
## Git Clone Repositories

+ DevOps team created a new Git repository last week; however, as of now no team is using it. The Nautilus application development team recently asked for a copy of that repo on Storage server in Stratos DC. Please clone the repo as per details shared below:
+ The repo that needs to be cloned is /opt/news.git
+ Clone this git repository under /usr/src/kodekloudrepos directory. Please do not try to make any changes in the repo.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos
  $ sudo git clone /opt/news.git 
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
---
## Git Install and Create Repository

+ The Nautilus development team shared with the DevOps team requirements for new application development, setting up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:
+ Install git package using yum on Storage server.
+ After that, create/init a git repository named /opt/news.git (use the exact name as asked and make sure not to create a bare repository).

###### Solution:
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ sudo yum install -y git
  $ sudo git init /opt/news.git
  $ ls -a /opt/news.git
  ```
---
## Git Create Branches

+ Nautilus developers are actively working on one of the project repositories, /usr/src/kodekloudrepos/demo. Recently, they decided to implement some new features in the application, and they want to maintain those new changes in a separate branch. Below are the requirements that have been shared with the DevOps team:
+ On Storage server in Stratos DC create a new branch xfusioncorp_demo from master branch in /usr/src/kodekloudrepos/demo git repo.
+ Please do not try to make any changes in the code.

###### Solution:
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/demo
  $ sudo git branch -a
  $ sudo git checkout master
  $ sudo git branch -a
  $ sudo git branch xfusioncorp_demo
  $ sudo git checkout xfusioncorp_demo
  $ sudo git branch -a
  ```
---
## Git Merge Branches

+ The Nautilus application development team has been working on a project repository /opt/demo.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with DevOps team:
+ Create a new branch nautilus in /usr/src/kodekloudrepos/demo repo from master and copy the /tmp/index.html file (present on storage server itself) into the repo. Further, add/commit this file in the new branch and merge back that branch into master branch. Finally, push the changes to the origin for both of the branches.

###### Solution
+ ```shell
  $ cd /usr/src/kodekloudrepos/demo
  $ sudo git branch -a
  $ sudo git branch nautilus
  $ sudo git checkout nautilus
  $ sudo cp /tmp/index.html .
  $ sudo git add *
  $ sudo git commit -m "kke"
  $ sudo git checkout master
  $ sudo git pull origin master
  $ sudo git merge nautilus
  $ sudo git push origin master
  $ sudo git branch -a
  ```