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

1. *Architecture overview*
2. *Scripts*
   1. main
   2. AWS Credentials Automation
   3. SSH Key Generator
   4. Setup Script
3. *Utilizing Terraform Scripts*
   1. The Main Configuration
   2. Allocating Instances
   3. Configuring Security Groups
   4. SSH Keys
4. *Implementing Ansible Playbooks*
   1. Configuring Instances with Ansible
   2. Installing Jenkins with Ansible
   3. Setting Up Jenkins using Ansible
   4. Installing Plugins with Ansible
---
## Architecture Overview:
![](/files/proj1dso.png#center)
We utilized Terraform to allocate two instances: one for the Jenkins master and another for the Jenkins agent. After allocating the instances, Ansible was employed to configure these instances by installing necessary dependencies and setting up Jenkins on them.

Here's a high-level overview of the process:
+ *Terraform*: 
  + Used to provision and allocate two instances in the cloud environment.
  + One instance is designated as the Jenkins master node.
  + The second instance serves as the Jenkins agent node.
+ *Ansible*: 
  + Performs configuration management and automation tasks on the instances provisioned by Terraform.
  + Installs required software dependencies on both the Jenkins master and agent nodes.
  + Sets up Jenkins on the respective nodes, ensuring they are ready to be used for continuous integration and continuous deployment (CI/CD) processes.
> The combination of Terraform and Ansible streamlines the process of infrastructure provisioning and configuration, enabling efficient setup and management of Jenkins in the cloud environment. This approach promotes scalability, flexibility, and automation, making it easier to manage and maintain the Jenkins infrastructure.
---
## Scripts
#### Main Configuration:
The main script served as the central entry point for coordinating various automation tasks and orchestrating the setup process.
> https://raw.githubusercontent.com/chxmxii/DevSecOps_Pipeline/main/provisioning_script.sh

#### AWS Credentials Automation:
This script automated the setup and configuration of AWS credentials, facilitating seamless integration with AWS services without manual intervention.
+ ```shell
  #!/bin/bash

  RED='\e[31m' 
  GREEN='\033[0;32m'

  read -p "Do you want to save your AWS credentials? (y/n) " confirm

  if [ "$confirm" = "y" ]; then

    read -p "Enter your AWS Access Key ID: " aws_access_key_id
    read -p "Enter your AWS Secret Access Key: " aws_secret_access_key
    read -p "Enter your AWS Session Token: " aws_session_token

    mkdir -p ~/.aws
    cat > ~/.aws/credentials <<EOL
  [default]
  aws_access_key_id = $aws_access_key_id
  aws_secret_access_key = $aws_secret_access_key
  aws_session_token = $aws_session_token
  EOL

    chmod 600 ~/.aws/credentials
    echo " "
    echo " "
    echo -e "${GREEN}Credentials succesfully saved to ~/.aws/credentials"
  else
    echo -e "${RED}skipped..."
    echo " "
    exit 0
  fi
  ```
#### SSH Key Generator:
With this script, we automatically generated and managed SSH key pairs, enhancing security and enabling secure communication between instances.
+ ```shell
  #!/bin/bash


  RED='\e[31m' 
  GREEN='\033[0;32m'

  read -p "Do you want to generate SSH keys for jenkins and prod? (y/n): " answer

  keys=(jenkins prod)

  if [[ "$answer" == "y" ]]; then
      if [ ! -d ~/.ssh ]; then
          mkdir -p ~/.ssh
      fi

      for id in "${keys[@]}"; do
          ssh-keygen -t rsa -b 4096 -f ~/.ssh/$id -N ''
      done

      for id in "${keys[@]}"; do
          echo "${GREEN}Generated ssh keys for $id:"
      done
  else
      echo -e "${RED}SSH key generation cancelled."
      exit 0
  fi
  ```

### Setup Script:
The setup script was a comprehensive automation tool that handled tasks such as installing dependencies, configuring settings, and deploying necessary configurations during the initial setup phase.
+ ```shell
  #!/bin/bash

  # Set versions for tools
  aws_version="2.9.23"
  terraform_version="1.4.5"
  ansible_version="latest"
  vault_version="1.13.1"

  if command -v docker-compose &>/dev/null; then
    echo "docker-compose is already installed"
  else
    sudo yum install docker -y
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo docker run hello-world
    sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    docker-compose --version
    sudo systemctl start docker.service
  fi

  # Check if AWS CLI is already installed
  if command -v aws &>/dev/null; then
    echo "AWS CLI is already installed"
  else
    # Download AWS CLI
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${aws_version}.zip" -o "setup/awscliv2.zip"
    unzip setup/awscliv2.zip
    sudo ./setup/aws/install
  fi

  # Check if Terraform is already installed
  if command -v terraform &>/dev/null; then
    echo "Terraform is already installed"
  else
    # Download Terraform
    curl "https://releases.hashicorp.com/terraform/${terraform_version}/terraform_${terraform_version}_linux_amd64.zip" -o "setup/terraform.zip"
    unzip setup/terraform.zip
    sudo mv setup/terraform /usr/local/bin/
  fi

  # Check if Ansible is already installed
  if command -v ansible &>/dev/null; then
    echo "Ansible is already installed"
  else
    # Download Ansible
    sudo apt update
    sudo apt install -y ansible="${ansible_version}"
  fi

  # Check if Vault is already installed
  #if command -v vault &>/dev/null; then
  #  echo "Vault is already installed"
  #else
    # Download Vault
  # curl "https://releases.hashicorp.com/vault/${vault_version}/vault_${vault_version}_linux_amd64.zip" -o "setup/vault.zip"
  # unzip setup/vault.zip
  #  sudo mv setup/vault /usr/local/bin/
  #fi

  # Verify installation
  aws --version
  terraform --version
  ansible --version
  vault --version
  docker-compose --version
  ```
---
## Utilizing Terraform Scripts

#### The Main Configuration
+ ```json
  //Specifies the required provider and its version for AWS.
  terraform {
    required_providers {
      aws = {
        source  = "hashicorp/aws"
        version = "4.54.0"
      }
    }
    
    //Configures the backend to store Terraform state data locally.
    backend local {}
  }

  //Configures the AWS provider with the region set to "us-east-1".
  provider "aws" {
    region = "us-east-1"
  }

  //Declares a local variable `vpc_id` with an empty string as its initial value.
  //Local variables are placeholders that can be used to reference data within the Terraform configuration.
  locals {
    vpc_id = ""
  }

  //Retrieves information about the AWS VPC using the specified `vpc_id`.
  //The data block allows access to existing data in the AWS environment without creating or modifying resources.
  data "aws_vpc" "main" {
    id = "${local.vpc_id}"
  }
  ``` 
#### Allocating Instances
+ ```json
  // Creates an AWS EC2 instance resource named "production".
  // This instance will be based on the specified AMI (Amazon Machine Image).
  resource "aws_instance" "production" {
    ami           = "ami-0dfcb1ef8550277af"
    instance_type = "t2.micro"
    key_name      = "${aws_key_pair.prod-key.key_name}" // Specifies the key pair name to be used for SSH access.
    vpc_security_group_ids = [aws_security_group.sg_devsecops.id] // Associates the instance with the specified security group.
    tags = {
      Name = "Production" // Assigns a tag to the instance for identification purposes.
    }
  }

  // Creates another AWS EC2 instance resource named "jenkins".
  // This instance will also be based on the specified AMI.
  resource "aws_instance" "jenkins" {
    ami           = "ami-0dfcb1ef8550277af"
    instance_type = "t2.small"
    key_name      = "${aws_key_pair.jenkins-key.key_name}" // Specifies the key pair name for SSH access to this instance.
    vpc_security_group_ids = [aws_security_group.sg_devsecops.id] // Associates the instance with the same security group as above.
    tags = {
      Name = "Jenkins" // Assigns a tag to the instance for identification purposes.
    }
  }
  ```
#### Configuring Security Groups
+ ```Json
  // Creates an AWS security group resource named "sg_devsecops".
  // The security group is associated with the VPC identified by the specified data source "aws_vpc.main.id".
  resource "aws_security_group" "sg_devsecops" {
    name        = "sg_DevSecOps" // Specifies the name of the security group.
    description = "DevSecOps Security Group" // Provides a description for the security group.
    vpc_id      = data.aws_vpc.main.id // Associates the security group with the VPC specified in the data block "aws_vpc.main".

    ingress = [
      {
        description      = "HTTP" // Description of the ingress rule.
        from_port        = 8080 // The starting port for incoming traffic.
        to_port          = 8080 // The ending port for incoming traffic.
        protocol         = "tcp" // The protocol (TCP) for incoming traffic.
        cidr_blocks      = ["0.0.0.0/0"] // Allows incoming traffic from any IPv4 address.
        ipv6_cidr_blocks = [] // No incoming traffic allowed from IPv6 addresses.
        prefix_list_ids  = [] // No prefix list IDs defined.
        security_groups = [] // No other security groups allowed as the source.
        self = false // The security group itself is not allowed as the source.
      },
      {
        description      = "SSH" // Description of the ingress rule.
        from_port        = 22 // The starting port for SSH incoming traffic.
        to_port          = 22 // The ending port for SSH incoming traffic.
        protocol         = "tcp" // The protocol (TCP) for SSH traffic.
        cidr_blocks      = ["0.0.0.0/0"] // Allows incoming SSH traffic from any IPv4 address.
        ipv6_cidr_blocks = [] // No incoming SSH traffic allowed from IPv6 addresses.
        prefix_list_ids  = [] // No prefix list IDs defined.
        security_groups = [] // No other security groups allowed as the source.
        self = false // The security group itself is not allowed as the source.
      }
    ]

    egress = [
      {
        description = "outgoing traffic" // Description of the egress rule.
        from_port        = 0 // The starting port for outgoing traffic.
        to_port          = 0 // The ending port for outgoing traffic.
        protocol         = "-1" // The protocol (-1 indicates all protocols) for outgoing traffic.
        cidr_blocks      = ["0.0.0.0/0"] // Allows outgoing traffic to any IPv4 address.
        ipv6_cidr_blocks = ["::/0"] // Allows outgoing traffic to any IPv6 address.
        prefix_list_ids  = [] // No prefix list IDs defined.
        security_groups = [] // No other security groups allowed as the destination.
        self = false // The security group itself is not allowed as the destination.
      }
    ]
  }
  ```
#### SSH Keys
+ ```json
  // Creates an AWS key pair resource named "jenkins-key".
  // The key pair will have the name "jenkins-key" and use the public key from the file "~/.ssh/jenkins.pub".
  resource "aws_key_pair" "jenkins-key" {
    key_name   = "jenkins-key"
    public_key = file("${pathexpand("~/.ssh/jenkins.pub")}") // Specifies the path to the public key file for the key pair.
  }

  // Creates another AWS key pair resource named "prod-key".
  // The key pair will have the name "prod-key" and use the public key from the file "~/.ssh/prod.pub".
  resource "aws_key_pair" "prod-key" {
    key_name   = "prod-key"
    public_key = file("${pathexpand("~/.ssh/prod.pub")}") // Specifies the path to the public key file for the key pair.
  }
  ```

> Next time, we will explore how we utilized Ansible to configure VMs, install, and set up Jenkins. Thanks for following up!!