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
