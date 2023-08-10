---
author:
  name: "chxmxii_"
date: 2023-08-01
tags:
  - DevOps
  - Sysadmin
type:
- post
- posts
title: KodeKloud Engineer Ansible Challenges 
weight: 10
series:
- Hugo 101
---
## Ansible Unarchive Module	

+ One of the DevOps team members has created an ZIP archive on jump host in Stratos DC that needs to be extracted and copied over to all app servers in Stratos DC itself. Because this is a routine task, the Nautilus DevOps team has suggested automating it. We can use Ansible since we have been using it for other automation tasks. Below you can find more details about the task:
+ We have an inventory file under /home/thor/ansible directory on jump host, which should have all the app servers added already.
+ There is a ZIP archive /usr/src/devops/nautilus.zip on jump host.
+ Create a playbook.yml under /home/thor/ansible/ directory on jump host itself to perform the below given tasks.
+ Unzip /usr/src/devops/nautilus.zip archive in /opt/devops/ location on all app servers.
+ Make sure the extracted data must has the respective sudo user as their user and group owner, i.e tony for app server 1, steve for app server 2, banner for app server 3.
+ The extracted data permissions must be 0644
+ Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.

###### Solution:
+ ```Shell
  #verify the connectivity 
  ansible all -m ping -i inventory
  #create a playbook.yml
  #run the playbook
  ansible-playbook -i inventory playbook.yml
  ```
+ ```yaml
  ---
  - hosts: all
    become: true
    tasks:
      - unarchive:
          src: /usr/src/sysops/xfusion.zip
          dest: /opt/sysops/
          mode: '0744'
          owner: tony
          group: tony
        when: ansible_hostname == "stapp01"
      - unarchive:
          src: /usr/src/sysops/xfusion.zip
          dest: /opt/sysops/
          mode: '0744'
          owner: steve
          group: steve
        when: ansible_hostname == "stapp02"
      - unarchive:
          src: /usr/src/sysops/xfusion.zip
          dest: /opt/sysops/
          mode: '0744'
          owner: banner
          group: banner
        when: ansible_hostname == "stapp03"
  ...
  ```
---
## Ansible Facts Gathering

+ The Nautilus DevOps team is trying to setup a simple Apache web server on all app servers in Stratos DC using Ansible. They also want to create a sample html page for now with some app specific data on it. Below you can find more details about the task.
+ You will find a valid inventory file /home/thor/playbooks/inventory on jump host (which we are using as an Ansible controller).
+ Create a playbook index.yml under /home/thor/playbooks directory on jump host. Using blockinfile Ansible module create a file facts.txt under /root directory on all app servers and add the following given block in it. You will need to enable facts gathering for this task.
Ansible managed node IP is <default ipv4 address>
(You can obtain default ipv4 address from Ansible's gathered facts by using the correct Ansible variable while taking into account Jinja2 syntax)
+ Install httpd server on all apps. After that make a copy of facts.txt file as index.html under /var/www/html directory. Make sure to start httpd service after that.
Note: Do not create a separate role for this task, just add all of the changes in index.yml playbook.

###### Solution:
+ ```yaml
  ---
  - hosts: all
    become: true
    gather_facts: true
    tasks:
      - file:
          path: /root/facts.txt
          state: touch
      - blockinfile:
          dest: /root/facts.txt
          block: "Ansible managed node IP is {{ansible_default_ipv4['address']}}"
      - package:
          name: httpd
          state: installed
      - copy:
          remote_src: yes
          src: /root/facts.txt
          dest: /var/www/html/index.html
      - service:
          name: httpd
          state: started  
  ```
---

---
## Puppet Create Symlinks

+ Some directory structure in the Stratos Datacenter needs to be changed, there is a directory that needs to be linked to the default Apache document root. We need to accomplish this task using Puppet, as per the instructions given below:
+ Create a puppet programming file official.pp under /etc/puppetlabs/code/environments/production/manifests directory on puppet master node i.e on Jump Server. Within that define a class symlink and perform below mentioned tasks:
+ Create a symbolic link through puppet programming code. The source path should be /opt/itadmin and destination path should be /var/www/html on Puppet agents 2 i.e on App Servers 2.
Create a blank file media.txt under /opt/itadmin directory on puppet agent 2 nodes i.e on App Servers 2.
+ Notes:
  + Please make sure to run the puppet agent test using sudo on agent nodes, otherwise you can face certificate issues. In that case you will have to clean the certificates first and then you will be able to run the puppet agent test. 
  + Before clicking on the Check button please make sure to verify puppet server and puppet agent services are up and running on the respective servers, also please make sure to run puppet agent test to apply/test the changes manually first.
+ P lease note that once lab is loaded, the puppet server service should start automatically on puppet master server, however it can take upto 2-3 minutes to start.

###### Solution
+ ```shell
  sudo vi /etc/puppetlabs/code/environments/production/manifests/official.pp
  #verify the syntax
  puppet parser validate /etc/puppetlabs/code/environments/production/manifests/official.pp
  #run on the agent 2
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02
  sudo puppet agent -tv
  #verify
  ls -lrt /var/www/html
  ls -lrt /opt/itadmin
  ```
+ ```js
  class symlink {
    //create sym link
    file{'/opt/itadmin':
      ensure => 'link',
      target => '/var/www/html',
    }
    //create file media.txt
    file{'/opt/itadmin/media.txt':
      ensure => 'present';
    }
  }

  node 'stapp01.stratos.xfusioncorp.com', 'stapp02.stratos.xfusioncorp.com', 'stapp03.stratos.xfusioncorp.com' {
    include symlink
  }
  ```
---
## Ansible Basic Playbook

+ One of the Nautilus DevOps team members was working on to test an Ansible playbook on jump host. However, he was only able to create the inventory, and due to other priorities that came in he has to work on other tasks. Please pick up this task from where he left off and complete it. Below are more details about the task:
+ The inventory file /home/thor/ansible/inventory seems to be having some issues, please fix them. The playbook needs to be run on App Server 2 in Stratos DC, so inventory file needs to be updated accordingly.
+ Create a playbook /home/thor/ansible/playbook.yml and add a task to create an empty file /tmp/file.txt on App Server 2.
+ Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please mak
  
###### Solution:
+ ```shell
  #fix the inventory file by adding the ssh user and sshpass
  stapp02 ansible_host=172.16.238.11 ansible_ssh_user=steve ansible_ssh_pass=Am3ric@ #remote_user=steve
  #verify
  ansible stapp02 -m ping -i ansible/inventory
  #now lets write the playbook
  vi ansible/playboo.yml
  #run the playbook
  ansible-playbook playbook.yml -i inventory
  ```
+ ```yaml
  ---
  - hosts: stapp02
    tasks:
      - file:
          dest: /tmp/file.txt
          state: touch
  ```
---
##

+ xFusionCorp Industries uses some monitoring tools to check the status of every service, application, etc running on the systems. Recently, the monitoring system identified that Apache service is not running on some of the Nautilus Application Servers in Stratos Datacenter.
  1. Identify the faulty Nautilus Application Server and fix the issue. Also, make sure Apache service is up and running on all Nautilus Application Servers. Do not try to stop any kind of firewall that is already running.
  2. Apache is running on 3002 port on all Nautilus Application Servers and its document root must be /var/www/html on all app servers.
  3. Finally you can test from jump host using curl command to access Apache on all app servers and it should be reachable and you should get some static page. E.g. curl http://172.16.238.10:3002/
  
###### Soluiton:
+ ```Shell
  sshpass -p <password> ssh -o StrictHostKeyChecking=no <username>@<hostname>
  sudo su -
  systemctl start httpd
  httpd -t
  vi +<line> <configfile> #e.g vi +34 /etc/httpd/conf/httpd.conf and fix the line!
  systemctl start httpd
  systemctl status httpd
  systemctl enable --now httpd
  curl <hostname>:3002
  ```