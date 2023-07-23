---
author:
  name: "chxmxii_"
date: 2022-09-10
tags:
  - aws
  - Jenkins
  - terraform
  - ansible
  - SAST
  - DAST
linktitle: 
type:
- post
- posts
title: Automating the implementation of a DevSecOps Pipeline - PART 01
weight: 10
series:
- Hugo 101
---
---
> Picture this: software development practices evolving like a Pokémon on steroids, giving birth to the legendary concepts of DevOps and its sidekick, DevSecOps. They're like Batman and Robin, but for software development. DevOps focuses on collaboration and integration between development and operations teams, turbocharging software delivery speed and reliability. And then, DevSecOps enters the scene, adding a sprinkle of security practices throughout the entire software development lifecycle. It's like adding a secret agent to the mix – shaken, not stirred.

##### *Table of Contents*

1. Architecture overview
   1. Tools & Technologies
2. Utilizing Terraform Scripts
   1. The Main Configuration
   2. Allocating Instances
   3. Configuring Security Groups
   4. Managing SSH Keys
3. Implementing Ansible Playbooks
   1. Configuring Instances with Ansible
   2. Installing Jenkins with Ansible
   3. Setting Up Jenkins using Ansible
   4. Installing Plugins with Ansible
---
## Introduction to the Architecture:
[](../files/proj1dso.png)

