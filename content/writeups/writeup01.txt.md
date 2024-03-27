---
author:
  name: "chxmxii_"
date: 2023-08-10
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

The Nautilus development team shared requirements with the DevOps team regarding new application development.—specifically, they want to set up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:
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

DevOps team created a new Git repository last week; however, as of now no team is using it. The Nautilus application development team recently asked for a copy of that repo on Storage server in Stratos DC. Please clone the repo as per details shared below:
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

There is a Git server used by the Nautilus project teams. Recently a new developer Jon joined the team and needs to start working on a project. To do so, he needs to fork an existing Git repository. Below you can find more details:
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

The Nautilus development team started with new project development. They have created different Git repositories to manage respective project's source code. One of the repositories /opt/cluster.git was created recently. The team has given us a sample index.html file that is currently present on jump host under /tmp directory. The repository has been cloned at /usr/src/kodekloudrepos on storage server in Stratos DC.
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

Nautilus developers are actively working on one of the project repositories, /usr/src/kodekloudrepos/blog. They were doing some testing and created few test branches, now they want to clean those test branches. Below are the requirements that have been shared with the DevOps team:
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

The Nautilus development team shared with the DevOps team requirements for new application development, setting up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:
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

Nautilus developers are actively working on one of the project repositories, /usr/src/kodekloudrepos/demo. Recently, they decided to implement some new features in the application, and they want to maintain those new changes in a separate branch. Below are the requirements that have been shared with the DevOps team:
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

The Nautilus application development team has been working on a project repository /opt/demo.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with DevOps team:
+ Create a new branch nautilus in /usr/src/kodekloudrepos/demo repo from master and copy the /tmp/index.html file (present on storage server itself) into the repo. Further, add/commit this file in the new branch and merge back that branch into master branch. Finally, push the changes to the origin for both of the branches.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
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
---
## Git Manage Remotes

The xFusionCorp development team added updates to the project that is maintained under /opt/cluster.git repo and cloned under /usr/src/kodekloudrepos/cluster. Recently some changes were made on Git server that is hosted on Storage server in Stratos DC. The DevOps team added some new Git remotes, so we need to update remote on /usr/src/kodekloudrepos/cluster repository as per details mentioned below:
+ In /usr/src/kodekloudrepos/cluster repo add a new remote dev_cluster and point it to /opt/xfusioncorp_cluster.git repository.
+ There is a file /tmp/index.html on same server; copy this file to the repo and add/commit to master branch.
+ Finally push master branch to this new remote origin.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/cluster/
  $ sudo git remote add dev_cluster /opt/xfusioncorp_cluster.git
  $ sudo cp /tmp/index.html .
  $ sudo git branch -a
  $ sudo git add *
  $ sudo git commit -m "kke"
  $ sudo git push -uf dev_cluster master
  $ sudo git branch -a
  ```
---
## Git Revert Some Changes

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/beta present on Storage server in Stratos DC. However, they reported an issue with the recent commits being pushed to this repo. They have asked the DevOps team to revert repo HEAD to last commit. Below are more details about the task:
+ In /usr/src/kodekloudrepos/beta git repository, revert the latest commit ( HEAD ) to the previous commit (JFYI the previous commit hash should be with initial commit message ).
+ Use revert beta message (please use all small letters for commit message) for the new revert commit.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/beta/
  $ sudo git log
  $ sudo git revert HEAD~1
  $ sudo git revert 8f7896cd77c3204d4f111e78652958c386d2b241
  $ sudo git add *
  $ sudo git commit -m "revert beta"
  $ sudo git push
  ```
---
## Git Cherry Pick

The Nautilus application development team has been working on a project repository /opt/games.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with the DevOps team:
+ There are two branches in this repository, master and feature. One of the developers is working on the feature branch and their work is still in progress, however they want to merge one of the commits from the feature branch to the master branch, the message for the commit that needs to be merged into master is Update info.txt. Accomplish this task for them, also remember to push your changes eventually.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/game
  $ sudo git branch -a
  $ sudo git checkout master
  $ sudo git loge
  $ sudo git log
  $ sudo git branch -a
  $ sudo git checkout master
  $ sudo git cherry-pick 97248b4fbc64a45b0d6251f2dfaafd275639b013
  $ sudo git branch -a
  $ sudo git log
  $ sudo git push
  ```
---
## Manage Git Pull Requests

Max want to push some new changes to one of the repositories but we don't want people to push directly to master branch, since that would be the final version of the code. It should always only have content that has been reviewed and approved. We cannot just allow everyone to directly push to the master branch. So, let's do it the right way as discussed below:
+ SSH into storage server using user max, password Max_pass123 . There you can find an already cloned repo under Max user's home.
+ Max has written his story about The 🦊 Fox and Grapes 🍇
+ Max has already pushed his story to remote git repository hosted on Gitea branch story/fox-and-grapes
+ Check the contents of the cloned repository. Confirm that you can see Sarah's story and history of commits by running git log and validate author info, commit message etc.
+ Max has pushed his story, but his story is still not in the master branch. Let's create a Pull Request(PR) to merge Max's story/fox-and-grapes branch into the master branch
+ Click on the Gitea UI button on the top bar. You should be able to access the Gitea page.
+ UI login info:
  ```text
  - Username: max
  - Password: Max_pass123
  PR title : Added fox-and-grapes story
  PR pull from branch: story/fox-and-grapes (source)
  PR merge into branch: master (destination)
  ```
+ Before we can add our story to the master branch, it has to be reviewed. So, let's ask tom to review our PR by assigning him as a reviewer
+ Add tom as reviewer through the Git Portal UI
+ Go to the newly created PR
+ Click on Reviewers on the right
+ Add tom as a reviewer to the PR
+ Now let's review and approve the PR as user Tom
+ Login to the portal with the user tom
+ Logout of Git Portal UI if logged in as max
+ UI login info:
  ```text
    - Username: tom
    - Password: Tom_pass123
    PR title : Added fox-and-grapes story
    Review and merge it.
  ```  
+ Great stuff!! The story has been merged! 👏

###### Solution:
> Challenges inherently provide instruction; there's no requirement to reconfigure the solution, as it's already implemented at the GUI level."
---
## Git Hard Reset

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/news present on Storage server in Stratos DC. This was just a test repository and one of the developers just pushed a couple of changes for testing, but now they want to clean this repository along with the commit history/work tree, so they want to point back the HEAD and the branch itself to a commit with message add data.txt file. Find below more details:
+ In /usr/src/kodekloudrepos/news git repository, reset the git commit history so that there are only two commits in the commit history i.e initial commit and add data.txt file.
+ Also make sure to push your changes.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ ls
  $ sudo git log
  $ sudo git reset --hard 301e43558ad3be731e78e410777816cb9d9f15d7
  $ sudo git log
  $ sudo git push -uf  
  ```
---
## Git Clean

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/blog present on Storage server in Stratos DC. One of the developers mistakenly created a couple of files under this repository, but now they want to clean this repository without adding/pushing any new files. Find below more details:
+ Clean the /usr/src/kodekloudrepos/blog git repository without adding/pushing any new files, make sure git status is clean.

###### Solution

+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/blog
  $ sudo git status
  $ sudo git clean -f
  $ sudo git status
  ```
---
## Git Stash

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/demo present on Storage server in Stratos DC. One of the developers stashed some in-progress changes in this repository, but now they want to restore some of the stashed changes. Find below more details to accomplish this task:
+ Look for the stashed changes under /usr/src/kodekloudrepos/demo git repository, and restore the stash with stash@{1} identifier. Further, commit and push your changes to the origin.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /usr/src/kodekloudrepos/demo
  $ sudo git log
  $ sudo git branch -a
  $ sudo git stash list
  $ sudo git stash show
  $ sudo git stash pop stash@{1}
  $ sudo git add *
  $ sudo git commit 
  $ sudo git commit -m "kke"
  $ sudo git push
  ```
---
## Git Rebase

The Nautilus application development team has been working on a project repository /opt/beta.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with DevOps team:
+ One of the developers is working on feature branch and their work is still in progress, however there are some changes which have been pushed into the master branch, the developer now wants to rebase the feature branch with the master branch without loosing any data from the feature branch, also they don't want to add any merge commit by simply merging the master branch into the feature branch. Accomplish this task as per requirements mentioned.

###### Solution
+ ```shell
    $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
    $ cd /usr/src/kodekloudrepos/blog/
    $ sudo git rebase master feature
    $ sudo git push origin feature
    $ sudo git pull origin feature
    $ sudo git config pull.rebase true
    $ sudo git pull origin feature
    $ sudo git push origin feature 
  ```
---
## Manage Git Repositories

A new developer just joined the Nautilus development team and has been assigned a new project for which he needs to create a new repository under his account on Gitea server. Additionally, there is some existing data that need to be added to the repo. Below you can find more details about the task:
+ Click on the Gitea UI button on the top bar. You should be able to access the Gitea UI. Login to Gitea server using username max and password Max_pass123.
+ Create a new git repository story_media under max user.
+ SSH into storage server using user max and password Max_pass123 and clone this newly created repository under user max home directory i.e /home/max.
+ Copy all files from location /usr/devops to the repository and commit/push your changes to the master branch. The commit message must be "add stories" (must be done in single commit).
+ Create a new branch max_cluster from master.
+ Copy a file story-index-max.txt from location /tmp/stories/ to the repository. This file has a typo, which you can fix by changing the word Mooose to Mouse. Commit and push the changes to the newly created branch. Commit message must be "typo fixed for Mooose" (must be done in single commit).

###### Solution
+ ```shell
    $ sshpass -p Max_pass123 ssh -o StrictHostKeyChecking=no max@ststor01
    $ pwd
    $ git clone http://git.stratos.xfusioncorp.com/max/story_media.git
    $ cd story_media/
    $ cp /usr/devops/* .
    $ git add *; git commit -m "add stories"; git push
    $ git branch max_cluster
    $ cp /tmp/stories/story-index-max.txt .
    $ git checkout max_cluster
    $ vi story-index-max.txt 
    $ git branch -a
    $ git add *; git commit -m "typo fixed for Mooose"; git push
    $ git add story-index-max.txt; git commit -m "typo fixed for Mooose"; git push --set-upstream origin max_cluster
  ```
---
## Resolve Git Merge Conflicts

Sarah and Max were working on writting some stories which they have pushed to the repository. Max has recently added some new changes and is trying to push them to the repository but he is facing some issues. Below you can find more details:
+ SSH into storage server using user max and password Max_pass123. Under /home/max you will find the story-blog repository. Try to push the changes to the origin repo and fix the issues. The story-index.txt must have titles for all 4 stories. Additionally, there is a typo in The Lion and the Mooose line where Mooose should be Mouse.
+ Click on the Gitea UI button on the top bar. You should be able to access the Gitea page. You can login to Gitea server from UI using username sarah and password Sarah_pass123 or username max and password Max_pass123.

###### Solution
+ ```shell
    $ sshpass -p Max_pass123 ssh -o StrictHostKeyChecking=no max@ststor01
    $ pwd
    $ vi story-index.txt
    $ git pull origin master
    $ git add story-index.txt 
    $ git commit -m "fix typo and merge request"
    $ git push origin master
  ```
---
## Git Hook

The Nautilus application development team was working on a git repository /opt/demo.git which is cloned under /usr/src/kodekloudrepos directory present on Storage server in Stratos DC. The team want to setup a hook on this repository, please find below more details:
+ Merge the feature branch into the master branch`, but before pushing your changes complete below point.
+ Create a post-update hook in this git repository so that whenever any changes are pushed to the master branch, it creates a release tag with name release-2023-06-15, where 2023-06-15 is supposed to be the current date. For example if today is 20th June, 2023 then the release tag must be release-2023-06-20. Make sure you test the hook at least once and create a release tag for today's release.
+ Finally remember to push your changes.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ cd /opt/demo.git/hooks
  $ sudo cat <EOF>> post-update
    #!/bin/bash

    date_now=$(date +"%Y-%m-%d")

    if [ "$1" == "refs/heads/master" ]; then
            r_tag="release-$date_now"
            git tag $r_tag && git push --tags
    fi
  $ sudo chmod +x post-update
  $ cd /usr/src/kodekloudrepos/demo
  $ sudo git branch -a
  $ sudo checkout master
  $ sudo merge feature
  $ sudo git push origin master
  ```
---
## Git Setup from Scratch
Some new developers have joined xFusionCorp Industries and have been assigned Nautilus project. They are going to start development on a new application, and some pre-requisites have been shared with the DevOps team to proceed with. Please note that all tasks need to be performed on storage server in Stratos DC.
  + a. Install git, set up any values for user.email and user.name globally and create a bare repository /opt/official.git.
  + b. There is an update hook (to block direct pushes to the master branch) under /tmp on storage server itself; use the same to block direct pushes to the master branch in /opt/official.git repo.
  + c. Clone /opt/official.git repo in /usr/src/kodekloudrepos/official directory.
  + d. Create a new branch named xfusioncorp_official in repo that you cloned under /usr/src/kodekloudrepos directory.
  + e. There is a readme.md file in /tmp directory on storage server itself; copy that to the repo, add/commit in the new branch you just created, and finally push your branch to the origin.
  + f. Also create master branch from your branch and remember you should not be able to push to the master directly as per the hook you have set up.

###### Solution
+ ```shell
  $ sshpass -p Bl@kW ssh -o StrictHostKeyChecking=no natasha@ststor01
  $ sudo yum install git
  $ sudo git config --global user.name "chxmxii"
  $ sudo git config --global user.email "chxmxii@gmail.com"
  $ cd /opt
  $ sudo git init --bare official.git
  $ sudo cp /tmp/update .
  $ sudo git clone /opt/official.git
  $ sudo git checkout --orphan xfusioncorp_official
  $ sudo git branch -a
  $ sudo cp /tmp/readme.md .
  $ sudo git add .
  $ sudo git commit -m "Added README.md"
  $ sudo git push -u origin xfusioncorp_official
  $ sudo git checkout -b master
  ```
+ ```shell
  #VERIFY
  [natasha@ststor01 official]$ sudo git branch -a
  
  * master
    xfusioncorp_official
    remotes/origin/xfusioncorp_official
  
  [natasha@ststor01 official]$ sudo git push origin master
  
  Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
  remote: Manual pushes to the master branch is restricted!!
  remote: error: hook declined to update refs/heads/master
  To /opt/official.git
   ! [remote rejected] master -> master (hook declined)
  error: failed to push some refs to '/opt/official.git'
  ```
---
> DONE! THANK YOU @KK!