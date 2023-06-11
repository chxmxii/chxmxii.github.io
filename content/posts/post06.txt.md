---
author:
  name: "chxmxii_"
date: 2022-10-20
tags:
  - terraform
  - devops
  - cloud
linktitle: 
type:
- post
- posts
title: Terraform Challenge Series - Challenge 1
weight: 10
series:
- Hugo 101
---

In this challenge we will deploy several Kubernetes resources using terraform.
Utilize /root/terraform_challenge directory to store your Terraform configuration files.
Note: kubectl is already installed on the host, you can check your deployments in the usual way.
#### Architecture : 
![](/chall1.png#center)
#### Solution :

###### Controlplane
+ We will start by installing terraform in the controlplane node
+ ```Shell
  apt update
  apt install unzip -y
  curl -L -o /tmp/terraform_1.1.5_linux_amd64.zip https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_linux_amd64.zip
  unzip -d /usr/local/bin /tmp/terraform_1.1.5_linux_amd64.zip
  which terraform
  terrafomr --version
  ```
###### Kubernetes-provider 
+ for the kubernets provider we will configure it within provider.tf file.
+ You can refer to the documentation for this provider, simply go to [Terraform Registry](https://registry.terraform.io/) and search for hashicorp/kubernetes.
