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
  cd /root/terraform_challenge
  ```
---
###### Kubernetes-provider 
+ for the kubernets provider we will configure it within provider.tf file.
+ You can refer to the documentation for this provider, simply go to [Terraform Registry](https://registry.terraform.io/) and search for hashicorp/kubernetes.
+ Click on USE PROVIDER button and copy the snippet into provider.tf
+ ```Terraform
  terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
      version = "2.11.0"
    }
  }
}

provider "kubernetes" {
  config_path    = "/root/.kube/config"
}```
Now, we can initialize the provider
+ ```Shell
  Terraform init
  ```
---
###### frontend
+ Create a terraform resource frontend for kubernetes deployement, you can refere to the documentation for [Kubernetes_deployment](https://registry.terraform.io/providers/hashicorp/kubernetes/2.11.0/docs/resources/deployment).
+ If you are familiar with kubernetes, you can see that the resource shema is arranged similarly to the corresponding YAML manifest.
+ ```Terraform
    resource "kubernetes_deployment" "frontend" {
    metadata {
      name = "frontend"
      labels = {
        name = "frontend"
      }
    }
    spec {
      replicas = 4
      selector {
        match_labels = {
          name = "webapp"
        }
      }
      template {
        metadata {
          labels = {
            name = "webapp"
          }
        }
        spec {
          container {
            image = "kodekloud/webapp-color:v1"
            name  = "simple-webapp"
            port {
              container_port = 8080
            }
          }
        }
      }
    }
  }
  ```
---
###### webapp-service
+ Create terraform resouce webapp-service for kubernetes service, you can always refere to the provider documentation for [kubernetes_service](https://registry.terraform.io/providers/hashicorp/kubernetes/2.11.0/docs/resources/service).
+ ```Terraform
    resource "kubernetes_service" "webapp-service" {
    metadata {
      name = "webapp-service"
    }
    spec {
      selector = {
        name = kubernetes_deployment.frontend.spec.0.template.0.metadata.0.labels.name
      }
      port {
        port        = 8080
        target_port = 8080
        node_port   = 30080
      }

      type = "NodePort"
    }
  }
  ```
---
###### Deploy the kubernetes resources
+ ```Shell
  terraform plan
  terraform apply
  ```