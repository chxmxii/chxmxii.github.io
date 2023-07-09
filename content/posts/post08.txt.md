---
author:
  name: "chxmxii_"
date: 2023-07-09
tags:
  - DevOps
  - Sysadmin
type:
- post
- posts
title: KodeKloud Engineer DevOps Engineer Challenges - P1
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