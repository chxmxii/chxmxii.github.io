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
## Kuberentes time check pod

+ The Nautilus DevOps team want to create a time check pod in a particular Kubernetes namespace and record the logs. This might be initially used only for testing purposes, but later can be implemented in an existing cluster. Please find more details below about the task and perform it.
+ Create a pod called time-check in the datacenter namespace. This pod should run a container called time-check, container should use the busybox image with latest tag only and remember to mention tag i.e busybox:latest.
+ Create a config map called time-config with the data TIME_FREQ=11 in the same namespace.
+ The time-check container should run the command: while true; do date; sleep $TIME_FREQ;done and should write the result to the location /opt/data/time/time-check.log. Remember you will also need to add an environmental variable TIME_FREQ in the container, which should pick value from the config map TIME_FREQ key.
+ Create a volume log-volume and mount the same on /opt/data/time within the container.
+ Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.
  
###### Solution:
+ ```shell
  vi ns.yml
  vi configmap.yml
  vi pod.yml
  kubectl get ns
  kubectl get configmap -n datacenter
  kubectl get pods -n datacenter
  ```
+ ```yaml
  #ns.yml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: datacenter
  #configmap.yml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: time-config
    namespace: datacenter
  data:
      TIME_FREQ: "11"
  #pod.yml
  apiVersion: v1
  kind: Pod
  metadata:
    name: time-check
    namespace: datacenter
  spec:
    containers:
      - name: time-check
        image: busybox:latest
        command: [ "sh", "-c", "while true; do date; sleep $TIME_FREQ;done >> /opt/data/time/time-check.log" ]
        env:
          - name: TIME_FREQ
            valueFrom:
              configMapKeyRef:
                name: time-config
                key: TIME_FREQ
        volumeMounts:
        - name: log-volume
          mountPath: /opt/data/time
    volumes:
      - name: log-volume
        emptyDir : {}
    restartPolicy: Never
  ```
--- 
## Docker Ports Mapping

+ The Nautilus DevOps team is planning to host an application on a nginx-based container. There are number of tickets already been created for similar tasks. One of the tickets has been assigned to set up a nginx container on Application Server 1 in Stratos Datacenter. Please perform the task as per details mentioned below:
+ a. Pull nginx:stable docker image on Application Server 1.
+ b. Create a container named cluster using the image you pulled.
+ c. Map host port 5000 to container port 80. Please keep the container in running state.

###### Solution:

+ ```Shell
  #ssh to app server1
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #pull the docker image
  sudo docker pull nginx:stable
  #run the container
  sudo docker run -itd --name cluster -p 5000:80 nginx:stable
  >itd - --interactive + --tty + --detach
  ```
---
## Deploy Apache Web Server on Kubernetes Cluster

+ There is an application that needs to be deployed on Kubernetes cluster under Apache web server. The Nautilus application development team has asked the DevOps team to deploy it. We need to develop a template as per requirements mentioned below:
+ Create a namespace named as httpd-namespace-devops.
+ Create a deployment named as httpd-deployment-devops under newly created namespace. For the deployment use httpd image with latest tag only and remember to mention the tag i.e httpd:latest, and make sure replica counts are 2.
+ Create a service named as httpd-service-devops under same namespace to expose the deployment, nodePort should be 30004.

###### Solution:
+ ```Shell
  touch {ns,dep,srv}.yml
  #verify later
  kubectl get deployment -n httpd-namespace-devops 
  kubectl get service -n httpd-namespace-devops
  ```
+ ```yaml
  #ns.yml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: httpd-namespace-devops
  #srv.yml
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: httpd-service-devops
    namespace: httpd-namespace-devops
  spec:
    type: NodePort
    selector:
      app: httpd_app_devops
    ports:
    - port: 80
      targetPort: 80
      nodePort: 3004
  #dep.yml
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: httpd-deployment-devops
    namespace: httpd-namespace-devops
  spec:
    replicas: 2
    selector:
      matchLabels:
        app: httpd_app_devops
    template:
      metadata:
        labels:
          app: httpd_app_devops
      spec:
        containers:
        - name: httpd-container
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
## Run a Docker Container:

+ Nautilus DevOps team is testing some applications deployment on some of the application servers. They need to deploy a nginx container on Application Server 3. Please complete the task as per details given below:
+ On Application Server 3 create a container named nginx_3 using image nginx with alpine tag and make sure container is in running state.

###### Solution:
+ ```shell
  #ssh to stapp03
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  #run the container
  docker run -d --name nginx_3 -p 8080:80 nginx:alpine
  #verify
  docker ps -a
  docker images
  curl -I localhost:8080
  ```
---
## Fix Issue with VolumeMounts in Kubernetes:

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
## Deploy Nginx and Phpfpm on Kubernetes

+ The Nautilus Application Development team is planning to deploy one of the php-based application on Kubernetes cluster. As per discussion with DevOps team they have decided to use nginx and phpfpm. Additionally, they shared some custom configuration requirements. Below you can find more details. Please complete the task as per requirements mentioned below:
+ Create a service to expose this app, the service type must be NodePort, nodePort should be 30012.
+ Create a config map nginx-config for nginx.conf as we want to add some custom settings for nginx.conf.
   + Change default port 80 to 8096 in nginx.conf.
   + Change default document root /usr/share/nginx to /var/www/html in nginx.conf.
   + Update directory index to index index.html index.htm index.php in nginx.conf.
+ Create a pod named nginx-phpfpm .
   + Create a shared volume shared-files that will be used by both containers (nginx and phpfpm) also it should be a emptyDir volume.
   + Map the ConfigMap we declared above as a volume for nginx container. Name the volume as nginx-config-volume, mount path should be /etc/nginx/nginx.conf and subPath should be nginx.conf
   + Nginx container should be named as nginx-container and it should use nginx:latest image. PhpFPM container should be named as php-fpm-container and it should use php:7.0-fpm image.
   + The shared volume shared-files should be mounted at /var/www/html location in both containers. Copy /opt/index.php from jump host to the nginx document root inside nginx container, once done you can access the app using App button on the top bar.
+ Before clicking on finish button always make sure to check if all pods are in running state.
+ You can use any labels as per your choice.
=> Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

###### Solution: 

+ ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: nginx-phpfpm
  spec:
    type: NodePort
    selector:
      app: nginx-phpfpm
      tier: back-end
    ports:
      - port: 8096
        targetPort: 8096
        nodePort: 30012 
  ```
+ ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: nginx-config
  data:
    nginx.conf: |
      events {} 
      http {
        server {
          listen 8096;
          index index.html index.htm index.php;
          root  /var/www/html;
          location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param REQUEST_METHOD $request_method;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass 127.0.0.1:9000;
          }
        }
      } 
  ```
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx-phpfpm
    labels:
      app: nginx-phpfpm
      tier: back-end
  spec:
    volumes:
      - name: shared-files
        emptyDir: {}
      - name: nginx-config-volume
        configMap:
          name: nginx-config
    containers:
      - name: nginx-container
        image: nginx:latest
        volumeMounts:
          - name: shared-files
            mountPath: /var/www/html
          - name: nginx-config-volume
            mountPath: /etc/nginx/nginx.conf
            subPath: nginx.conf
      - name: php-fpm-container
        image: php:7.0-fpm
        volumeMounts:
          - name: shared-files
            mountPath: /var/www/html
  ```
+ ```shell
  thor@jump_host ~$ kubectl apply -f .
  thor@jump_host ~$ kubectl get all
  NAME               READY   STATUS    RESTARTS   AGE
  pod/nginx-phpfpm   2/2     Running   0          5m

  NAME                   TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
  service/kubernetes     ClusterIP   10.96.0.1    <none>        443/TCP          76m
  service/nginx-phpfpm   NodePort    10.96.72.3   <none>        8096:30012/TCP   5m
  thor@jump_host ~$ kubectl cp /opt/index.php nginx-phpfpm:/var/www/html/ --container=nginx-container
  ```
---
## Deploy Node App on Kubernetes 

+ The Nautilus development team has completed development of one of the node applications, which they are planning to deploy on a Kubernetes cluster. They recently had a meeting with the DevOps team to share their requirements. Based on that, the DevOps team has listed out the exact requirements to deploy the app. Find below more details:
+ Create a deployment using gcr.io/kodekloud/centos-ssh-enabled:node image, replica count must be 2.
+ Create a service to expose this app, the service type must be NodePort, targetPort must be 8080 and nodePort should be 30012.
+ Make sure all the pods are in Running state after the deployment.
+ You can check the application by clicking on NodeApp button on top bar.
+ You can use any labels as per your choice.
=> Note: The kubectl on jump_host has been configured to work with the kubernetes cluster

###### Solution:
+ ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: node-deployment
    namespace: default
  spec:
    replicas: 2
    selector:
      matchLabels:
        app: node-app
    template:
      metadata:
        labels: 
          app: node-app
      spec:
      containers:
        - name: node-container
          image: gcr.io/kodekloud/centos-ssh-enabled:node
          ports:
            - containerPort: 80
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: node-service
    namespace: default
  spec:
    type: NodePort
    selector:
      app: node-app
    ports:
      - port: 80
        targetPort: 8080
        nodePort: 30012
  ```
+ ```shell
  thor@jump_host ~$ kubectl get pods
  NAME                              READY   STATUS    RESTARTS   AGE
  node-deployment-dd99d7b78-ftr2r   1/1     Running   0          2m2s
  node-deployment-dd99d7b78-wshn6   1/1     Running   0          2m2s
  thor@jump_host ~$ kubectl get svc
  NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
  kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        30m
  node-service   NodePort    10.96.174.163   <none>        80:30012/TCP   3m
  ```
---
## Docker Copy Operaton:

+ The Nautilus DevOps team has some conditional data present on App Server 1 in Stratos Datacenter. There is a container ubuntu_latest running on the same server. We received a request to copy some of the data from the docker host to the container. Below are more details about the task:
+ On App Server 1 in Stratos Datacenter copy an encrypted file /tmp/nautilus.txt.gpg from docker host to ubuntu_latest container (running on same server) in /usr/src/ location. Please do not try to modify this file in any way.
  
###### Solution

+ ```shell
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  docker ps
  docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/usr/src/
  docker exec <container_id> ls /usr/src/
  ```
---
## Docker Container Issue

+ There is a static website running within a container named nautilus, this container is running on App Server 1. Suddenly, we started facing some issues with the static website on App Server 1. Look into the issue to fix the same, you can find more details below:
+ Container's volume /usr/local/apache2/htdocs is mapped with the host's volume /var/www/html.
+ The website should run on host port 8080 on App Server 1 i.e command curl http://localhost:8080/ should work on App Server 1.
  
###### Solution:
+ ```Shell
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  #inspect container so it matches the req
  docker inspect <container_id>
  #list all containers
  docker ps -a
  #start the desired container
  docker start <docker_id>
  #verify
  curl localhost:8080
  ```
---

## Ansible Basic Playbook

+ One of the Nautilus DevOps team members was working on to test an Ansible playbook on jump host. However, he was only able to create the inventory, and due to other priorities that came in he has to work on other tasks. Please pick up this task from where he left off and complete it. Below are more details about the task:
+ The inventory file /home/thor/ansible/inventory seems to be having some issues, please fix them. The playbook needs to be run on App Server 2 in Stratos DC, so inventory file needs to be updated accordingly.
+ Create a playbook /home/thor/ansible/playbook.yml and add a task to create an empty file /tmp/file.txt on App Server 2.
+ Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please mak
  
###### Solution
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