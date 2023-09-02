---
author:
  name: "chxmxii_"
date: 2023-08-07
tags:
  - DevOps
  - Docker
type:
- post
- posts
title: KKE Docker Challenges 
weight: 10
series:
- Hugo 101
---
![](/files/Docker.png#center)

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
## Pull Docker Image

+ Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:
+ a. Pull busybox:musl image on App Server 1 in Stratos DC and re-tag (create new tag) this image as busybox:news

###### Solution:
+ ```Shell
  docker image ls 
  docker pull busybox:musl
  docker images
  docker tag busybox:musl busybox:news
  docker images
  ```

---
## Write a Docker File

+ As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects. Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file /opt/docker/Dockerfile (please keep D capital of Dockerfile) on App server 3 in Stratos DC and configure to build an image with the following requirements:
+ a. Use ubuntu as the base image.
+ b. Install apache2 and configure it to work on 5004 port. (do not update any other Apache configuration settings like document root etc).

###### Solution:
+ ```shell
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  cd /opt/docker/
  vi Dockerfile
  #build the image
  docker built -t apache .
  #run the container
  docker run -d -p 5004:5004 --name docker apache
  #test
  curl localhost:5004
  ```
+ ```Dockerfile
  FROM ubuntu
  RUN apt-get update && apt-get install -y apache2
  RUN sed -i 's/Listen 80/Listen 5004/g' /etc/apache2/ports.conf
  EXPOSE 5004
  CMD ["apache2ctl","-D","FOREGROUND"]
  ```
---
## Docker Update Permissions

+ One of the Nautilus project developers need access to run docker commands on App Server 2. This user is already created on the server. Accomplish this task as per details given below:
+ User mark is not able to run docker commands on App Server 2 in Stratos DC, make the required changes so that this user can run docker commands without sudo

###### Solution
+ ```Shell
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02
  sudo usermod -aG docker mark
  id mark
  su - mark
  id
  docker ps
  ```
---
## Docker EXEC Operations

+ One of the Nautilus DevOps team members was working to configure services on a kkloud container that is running on App Server 3 in Stratos Datacenter. Due to some personal work he is on PTO for the rest of the week, but we need to finish his pending work ASAP. Please complete the remaining work as per details given below:
+ a. Install apache2 in kkloud container using apt that is running on App Server 3 in Stratos Datacenter.
+ b. Configure Apache to listen on port 8084 instead of default http port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.
+ c. Make sure Apache service is up and running inside the container. Keep the container in running state at the end.

###### Solution
+ ```Shell
  sshpass -p BigGr33n  ssh -o StrictHostKeyChecking=no banner@stapp03
  docker ps
  docker exec -it kkloud apt-get install apache2 &&\
  > sed -i 's/Listen 80/Listen 8084/g' /etc/apache2/ports.conf &&\
  > service apache start &&\
  > curl localhost:8084
  docker inspect -f "{{ .NetworkSettings.IPAddress }}
  curl $ip:8084
  ```
---
## Create a Docker Image From Container

+ One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:
+ a. Create an image demo:datacenter on Application Server 3 from a container ubuntu_latest that is running on same server.
  
###### Solution
+ ```Shell
  sshpass -p BigGr33n  ssh -o StrictHostKeyChecking=no banner@stapp03
  docker ps
  docker image ls
  docker commit demo:datacenter ubuntu_latest
  docker image ls
  ```
---

## Create a Docker Network

+ The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:
+ a. Create a docker network named as blog on App Server 2 in Stratos DC.
+ b. Configure it to use bridge drivers.
+ c. Set it to use subnet 192.168.0.0/24 and iprange 192.168.0.2/24.

###### Solution
+ ```Shell
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02
  sudo su -
  docker network ls
  docker network create --driver=bridge --ip-range=192.168.0.2/24 --subnet=192.168.0.0/24 blog
  docker network inspect blog
      {
        "Name": "blog",
        "Id": "6cd0bae9b622286afb87eb284c36d14b20f23b242c69cae5bfede8b1b257191a",
        "Created": "2023-08-02T08:32:56.010259327Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/24",
                    "IPRange": "192.168.0.2/24"
                }
            ]
        },
      }
  docker network ls
  ```
---
## Docker Volumes Mapping

+ The Nautilus DevOps team is testing applications containerization, which issupposed to be migrated on docker container-based environments soon. In today's stand-up meeting one of the team members has been assigned a task to create and test a docker container with certain requirements. Below are more details:
+ a. On App Server 2 in Stratos DC pull nginx image (preferably latest tag but others should work too).
+ b. Create a new container with name beta from the image you just pulled.
+ c. Map the host volume /opt/security with container volume /usr/src/. There is an sample.txt file present on same server under /tmp; copy that file to /opt/security. Also please keep the container in running state.
+ ```Shell
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02
  sudo su
  docker pull nginx:latest #pull the nginx image
  docker run -d --name beta -v /opt/security:/usr/src/:Z nginx:latest
  cp /tmp/sample.txt /opt/security/ #copy the sample.txt file 
  docker exec -it beta cat /usr/src/sample.txt #verify
  ```
---
## Save Load and Transfer Docker Image

+ One of the DevOps team members was working on to create a new custom docker image on App Server 1 in Stratos DC. He is done with his changes and image is saved on same server with name demo:devops. Recently a requirement has been raised by a team to use that image for testing, but the team wants to test the same on App Server 3. So we need to provide them that image on App Server 3 in Stratos DC.
+ a. On App Server 1 save the image demo:devops in an archive.
+ b. Transfer the image archive to App Server 3.
+ c. Load that image archive on App Server 3 with same name and tag which was used on App Server 1.
+ Note: Docker is already installed on both servers; however, if its service is down please make sure to start it.

###### Solution
+ ```shell
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  docker images 
  docker save demo:devops -o /tmp/demo.tar
  scp /tmp/demo.tar banner@stapp03:/tmp/
  docker inspect --format "{{ .Config.Cmd }}" demo:devops #2verify
    [/bin/bash]
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  docker load -i /tmp/demo.tar
  docker inspect --format "{{ .Config.Cmd }}" demo:devops #2verify
    [/bin/bash]
  ```
---
## Write a Docker Compose File 

+ The Nautilus application development team shared static website content that needs to be hosted on the httpd web server using a containerised platform. The team has shared details with the DevOps team, and we need to set up an environment according to those guidelines. Below are the details:
+ a. On App Server 3 in Stratos DC create a container named httpd using a docker compose file /opt/docker/docker-compose.yml (please use the exact name for file).
+ b. Use httpd (preferably latest tag) image for container and make sure container is named as httpd; you can use any name for service.
+ c. Map 80 number port of container with port 5004 of docker host.
+ d. Map container's /usr/local/apache2/htdocs volume with /opt/itadmin volume of docker host which is already there. (please do not modify any data within these locations).

###### Solution
+ ```Shell
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  cd /opt/docker
  vi docker-compose.yml
  docker-compose up -d
  docker ps
  ```
+ ```yaml
  version: '3'
  services:
    httpd:
      image: httpd:latest
      container_name: httpd
      ports:
        - "5004:80"
      volumes:
        - /opt/itadmin:/usr/local/apache2/htdocs
  ```
---
## Resolve Dockerfile Issues

+ The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a Dockerfile on App Server 3 in Stratos DC. While working on it she ran into issues in which the docker build is failing and displaying errors. Look into the issue and fix it to build an image as per details mentioned below:
+ a. The Dockerfile is placed on App Server 3 under /opt/docker directory.
+ b. Fix the issues with this file and make sure it is able to build the image.
+ c. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html.
+ Note: Please note that once you click on FINISH button all existing images, the containers will be destroyed and new image will be built from your Dockerfile.

###### Solution

+ ```Shell
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03 
  cd /opt/docker
  vi Dockerfile
  ```
+ to solve the problem I have replaced all the lines under `FROM` with `RUN pwd;ls;ls conf.d;ls conf` to check which dir does exist conf.d/conf. 
+ ```Docker
  FROM httpd:2.4.43

  RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

  RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

  RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

  RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

  COPY certs/server.crt /usr/local/apache2/conf/server.crt

  COPY certs/server.key /usr/local/apache2/conf/server.key

  COPY html/index.html /usr/local/apache2/htdocs/
  ```
---
## Resolve Docker Compose Issues

+ The Nautilus DevOps team is working to deploy one of the applications on App Server 1 in Stratos DC. Due to a misconfiguration in the docker compose file, the deployment is failing. We would like you to take a look into it to identify and fix the issues. More details can be found below:
+ a. docker-compose.yml file is present on App Server 1 under /opt/docker directory.
+ b. Try to run the same and make sure it works fine.
+ c. Please do not change the container names being used. Also, do not update or alter any other valid config settings in the compose file or any other relevant data that can cause app failure.
+ Note: Please note that once you click on FINISH button all existing running/stopped containers will be destroyed, and your compose will be run.

###### Solution
+ ```Shell
  sshpass -p Ir0nM@n ssh -o StrictHostKeyChecking=no tony@stapp01
  cd /opt/docker
  sudo docker-compose up
  vi docker-compose.yml
  ```
+ ```yaml
  version: '2'
  services:
      web:
          build:
            context: app/
            dockerfile: Dockerfile
          container_name: python
          ports:
              - "5000:5000"
          volumes:
              - .:/code
          depends_on:
              - redis
          command: python app/app.py
      redis:
          image: redis
          container_name: redis
  ```
---
## Deploy an App on Docker Containers
 
+ The Nautilus Application development team recently finished development of one of the apps that they want to deploy on a containerized platform. The Nautilus Application development and DevOps teams met to discuss some of the basic pre-requisites and requirements to complete the deployment. The team wants to test the deployment on one of the app servers before going live and set up a complete containerized stack using a docker compose fie. Below are the details of the task:
+ On App Server 3 in Stratos Datacenter create a docker compose file /opt/finance/docker-compose.yml (should be named exactly).
+ The compose should deploy two services (web and DB), and each service should deploy a container as per details below:
+ For web service:
  + a. Container name must be php_web.
  + b. Use image php with any apache tag. Check here for more details.
  + c. Map php_web container's port 80 with host port 6100
  + d. Map php_web container's /var/www/html volume with host volume /var/www/html.
+ For DB service:
  + a. Container name must be mysql_web.
  + b. Use image mariadb with any tag (preferably latest). Check here for more details.
  + c. Map mysql_web container's port 3306 with host port 3306
  + d. Map mysql_web container's /var/lib/mysql volume with host volume /var/lib/mysql.
  + e. Set MYSQL_DATABASE=database_web and use any custom user ( except root ) with some complex password for DB connections.
+ After running docker-compose up you can access the app with curl command curl <server-ip or hostname>:6100/
For more details check here.
+ Note: Once you click on FINISH button, all currently running/stopped containers will be destroyed and stack will be deployed again using your compose file.
  
###### Soluiton
+ ```shell
  sshpass -p BigGr33n ssh -o StrcitHostKeyChecking=no banner@stapp03
  cd /opt/finance
  sudo vi docker-compose.yml
  sudo docker-compose up -d
  sudo docker ps
  curl localhost:6100
  curl stapp03:6100  
  ```
+ ```yaml
  version: "3"
  services:
    web:
      container_name: php_web
      image: php:7.3-apache-stretch
      ports:
        - "6100:80"
      volumes:
        - /var/www/html:/var/www/html
    DB:
      container_name: mysql_web
      image: mariadb:latest
      ports:
        - "3306:3306"
      volumes:
        - /var/lib/mysql:/var/lib/mysql
      environment:
        MYSQL_DATABASE: database_web
        MYSQL_ROOT_PASSWORD: r00t321
        MYSQL_USER: kodekloud
        MYSQL_PASSWORD: k0d3kl0ud3
  ```
---
## Docker Node App

+ There is a requirement to Dockerize a Node app and to deploy the same on App Server 3. Under /node_app directory on App Server 3, we have already placed a package.json file that describes the app dependencies and server.js file that defines a web app framework.
+ Create a Dockerfile (name is case sensitive) under /node_app directory:
  + Use any node image as the base image.
  + Install the dependencies using package.json file.
  + Use server.js in the CMD.
  + Expose port 5001.
+ The build image should be named as nautilus/node-web-app.
+ Now run a container named nodeapp_nautilus using this image.
+ Map the container port 5001 with the host port 8093.
+ Once deployed, you can test the app using a curl command on App Server 3:
  + `curl http://localhost:8093`

###### Solution
+ ```Shell
  sshpass -p BigGr33n ssh -o StrictHostKeyChecking=no banner@stapp03
  cd /node_app
  sudo vim Dockerfile
  docker build -t nautilus/node-web-app .
  docker run -d --name nodeapp_nautilus -p 8093:5001 nautilus/node-web-app
  curl localhost:8096
  ```
+ ```Docker
  FROM node:alpine
  WORKDIR /app
  COPY ["package.json","server.js","./"]
  RUN npm install
  EXPOSE 5001
  CMD ["node", "server.js"]
  ```
---
## Docker Python App

+ A python app needed to be Dockerized, and then it needs to be deployed on App Server 2. We have already copied a requirements.txt file (having the app dependencies) under /python_app/src/ directory on App Server 2. Further complete this task as per details mentioned below:
  + Create a Dockerfile under /python_app directory:
    + Use any python image as the base image.
    + Install the dependencies using requirements.txt file.
    + Expose the port 3002.
    + Run the server.py script using CMD.
  + Build an image named nautilus/python-app using this Dockerfile.
  + Once image is built, create a container named pythonapp_nautilus:
    + Map port 3002 of the container to the host port 8097.
  + Once deployed, you can test the app using curl command on App Server 2.

###### Solution:
+ ```shell
  sshpass -p Am3ric@ ssh -o StrictHostKeyChecking=no steve@stapp02
  cd /python_app
  sudo vi Dockerfile
  docker build -t nautilus/python-app .
  docker run -d --name pythonapp_nautilus -p 8097:3002 nautilus/python-app
  docker ps
  curl localhost:8097
  ```
+ ```Docker
  FROM python:3
  COPY src .
  RUN pip install --no-cache-dir -r requirements.txt
  EXPOSE 3002
  CMD ["pyhton","server.py"]
  ```
> DONE!!