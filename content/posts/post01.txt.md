---
author:
  name: "chxmxii_"
date: 2022-10-25
linktitle: 
tags:
 - DevOps
type:
- post
- posts
title: Understanding CI/CD
weight: 10
series:
- Hugo 101
---

![](/cicdlogo.png)


### 0 . 1 What is CI/CD :

- CI/CD is an automated and reliable process for software development and delivery.&*
- CI/CD’s frequent testing reduces code errors and defects.
- The main concepts attributed to CI/CD are continuos integration, delivery and deployment.

⇒ The main goal is to reduce the risk involved in deploying software.

### 0 . 2 What’s the difference between CI & CD & CD :

- Countinous integration is an automation process for developers, which is new code changes to an app are regularly built, tested and merged to a shared repository.
- Countinous delivery is a solution to the problem of poor visibility and commmunication between dev and business teams. its purpose is to ensure that it takes minimal effort to deploy new code.
- Countinous deployment means to automatically releasing a dev’s changes from the repository to produciton, where it is usable by customers.

![Untitled](/cicd.png)

### 0 . 3 Different stages :

CI/CD piple can be divided into four main stages :*

1. Source
2. Build
3. Test
4. Deployment

The stages are executed in a linear fashion, and each stage must be completed successfully before the next stage can begin.

![](/cicd2.png)

![](/cicd3.png)

### 0 . 3 . 1 Source Stage

First stage of any CI/CD pipeline. In this stage the pipleline will get trigged by any change in the program. this stage will cover version controlling and tracking changes.

If the automated workflow detects a change in the central repository, it will trigger tasks such as code compilation and unit testing.

**Example tools :**

- GIT
- Azure Repos
- AWS CodeCommit

### 0 . 3 . 2 Build Stage :

- The second stage of the pipeline you merge the source code and its dependencies.
- It is done mainly to build a runnable instance of software that you can potentially ship to the end-user.
- Failure to pass the build stage could indicate a fundamental issue in the underlying code.

Example tools : 

1. Jenkins
2. AWS CodeBuild
3. Azure Pipelines

### 0 . 3 . 3 Test Stage :

Test stage includes the execution of automated tests written by developers (integration testing, functional testing etc..) to validate the correctness of code and the behaviour of the software.

The main goal of this stage is to prevent software bugs from reaching end-users.

**Example Tools :** 

1. Puppeteer
2. Jest
3. Selenium.

### 0 . 3 . 4 Deploy stage :

Final stage of the pipeline, where your product goes live after passing the source,build and test stages successfully.

The deployment stage can include infra provisioning, config, and containerization using techs like terraform, puppet,docker and k8s.

**Example Tools :** 

1. Ansible
2. Chef
3. AWS Elastic beanstalk
4. AWS Code Deploy
5. Azure Pipelines - Deployment.