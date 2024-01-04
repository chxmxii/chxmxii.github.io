---
author:
  name: "chxmxii_"
date: 2023-08-25
tags:
  - DevOps
  - Sysadmin
type:
- post
- posts
title: KKE - Kubernetes Challenges 
weight: 10
series:
- Hugo 101
---
![](/files/kube.png#center)
---
## Create Pods in Kubernetes Cluster

+ The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform as they are planning to migrate most of their applications on Kubernetes platform. Recently one of the team members has been assigned a task to create a pod as per details mentioned below:
+ Create a pod named pod-httpd using httpd image with latest tag only and remember to mention the tag i.e httpd:latest.
+ Labels app should be set to httpd_app, also container should be named as httpd-container.

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: pod-httpd
    labels:
      app: httpd_app
  spec:
    containers:
      - name: httpd-container
        image: httpd:latest
  ```
---
## Create Deployments in Kubernetes Cluster

+ The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform, as they are planning to migrate most of their applications on Kubernetes. Recently one of the team members has been assigned a task to create a deployment as per details mentioned below:
+ Create a deployment named httpd to deploy the application httpd using the image httpd:latest (remember to mention the tag as well)

###### Solution
+ ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: httpd
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: httpd
    template: 
      metadata: 
        labels:
          app: httpd
      spec:
        containers:
          - name: httpd
            image: httpd:latest
  ```
---
## Create Namespaces in Kubernetes Cluster

+ The Nautilus DevOps team is planning to deploy some micro services on Kubernetes platform. The team has already set up a Kubernetes cluster and now they want set up some namespaces, deployments etc. Based on the current requirements, the team has shared some details as below:

+ Create a namespace named dev and create a POD under it; name the pod dev-nginx-pod and use nginx image with latest tag only and remember to mention tag i.e nginx:latest.

###### Solution

+ ```yaml
  ---
  apiVersion: v1
  kind: Namespace
  metadata:
    name: dev
  ---
  apiVersion: v1
  kind: Pod
  metadata:
    name: dev-nginx-pod
    namespace: dev
  spec:
    containers:
    - name: nginx-dev
      image: nginx:latest
  ```
---
## Set Limits for Resources in Kubernetes

+ Recently some of the performance issues were observed with some applications hosted on Kubernetes cluster. The Nautilus DevOps team has observed some resources constraints, where some of the applications are running out of resources like memory, cpu etc., and some of the applications are consuming more resources than needed. Therefore, the team has decided to add some limits for resources utilization. Below you can find more details.

+ Create a pod named httpd-pod and a container under it named as httpd-container, use httpd image with latest tag only and remember to mention tag i.e httpd:latest and set the following limits:
`Requests: Memory: 15Mi, CPU: 100m`
`Limits: Memory: 20Mi, CPU: 100m `

###### Solution:
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: httpd-pod
  spec:
    containers:
    -  name: httpd-container
      image: httpd:latest
      resources:
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits: 
          memory: "20Mi"
          cpu: "100m"
  ```
---
## Rolling Updates in Kubernetes

+ We have an application running on Kubernetes cluster using nginx web server. The Nautilus application development team has pushed some of the latest changes and those changes need be deployed. The Nautilus DevOps team has created an image nginx:1.19 with the latest changes.
+ Perform a rolling update for this application and incorporate nginx:1.19 image. The deployment name is nginx-deployment
+ Make sure all pods are up and running afte  r the update.

###### Solution
+ ```shell
  $ kubectl get all
  $ kubectl get deployment nginx-deployment -o yaml
  $ kubectl set image deployment/nginx-deployment nginx-container=nginx:1.19
  $ watch kubectl get pods 
  $ kubectl rollout status deployment nginx-deployment
  $ kubectl describe deployment nginx-deployment
  ```
---
## Rollback a Deployment in Kubernetes

+ This morning the Nautilus DevOps team rolled out a new release for one of the applications. Recently one of the customers logged a complaint which seems to be about a bug related to the recent release. Therefore, the team wants to rollback the recent release.
+ There is a deployment named nginx-deployment; roll it back to the previous revision.

###### Solution
+ ```shell
  $ kubectl get deployment
  $ kubectl get pods
  $ kubectl rollout undo deployment  nginx-deployment 
  $ kubectl rollout status deployment  nginx-deployment 
  ```
--- 
## Create Cronjobs in Kubernetes

+ There are some jobs/tasks that need to be run regularly on different schedules. Currently the Nautilus DevOps team is working on developing some scripts that will be executed on different schedules, but for the time being the team is creating some cron jobs in Kubernetes cluster with some dummy commands (which will be replaced by original scripts later). Create a cronjob as per details given below:
+ Create a cronjob named datacenter.
+ Set Its schedule to something like */8 * * * *, you set any schedule for now.
+ Container name should be cron-datacenter.
+ Use nginx image with latest tag only and remember to mention the tag i.e nginx:latest.
+ Run a dummy command echo Welcome to xfusioncorp!.
+ Ensure restart policy is OnFailure

###### Solution
+ ```yaml
  apiVersion: batch/v1
  kind: CronJob
  metadata:
    name: datacenter
  spec:
    schedule: "*/8 * * * *"
    jobTemplate: 
      spec: 
        template:
          spec:
            containers:
            - name: cron-datacenter
              image: nginx:latest
              command: 
              - /bin/sh
              - -c
              - echo Welcome to xfusioncorp!
            restartPolicy: OnFailure
  ```
---
## Countdown job in Kubernetes

+ The Nautilus DevOps team is working on to create few jobs in Kubernetes cluster. They might come up with some real scripts/commands to use, but for now they are preparing the templates and testing the jobs with dummy commands. Please create a job template as per details given below:
+ Create a job countdown-nautilus.
+ The spec template should be named as countdown-nautilus (under metadata), and the container should be named as container-countdown-nautilus
+ Use image debian with latest tag only and remember to mention tag i.e debian:latest, and restart policy should be Never.
+ Use command sleep 5

###### Solution:
+ ```yaml
  apiVersion: batch/v1
  kind: Job
  metadata:
    name: countdown-datacenter
  spec:
    template:
      metadata:
        name: countdown-datacenter
      spec:
        containers:
        -  name: container-countdown-datacenter
          image: centos:latest
          command: ["sleep", "5"]
        restartPolicy: Never 
  # kubectl apply -f job.yml
  # watch kubectl get jobs
  ```
---
## Troubleshoot Issue With Pods

+ One of the junior DevOps team members was working on to deploy a stack on Kubernetes cluster. Somehow the pod is not coming up and its failing with some errors. We need to fix this as soon as possible. Please look into it.
+ There is a pod named webserver and the container under it is named as nginx-container. It is using image nginx:latest
+ There is a sidecar container as well named sidecar-container which is using ubuntu:latest image.
+ Look into the issue and fix it, make sure pod is in running state and you are able to access the app.

+ ```shell 
  $ kubectl get pods
  $ kubectl describe pod/webserver
  $ kubectl edit pod/webserver
  $ watch kubectl get pods
  ```
---
## Update an Existing Deployment in Kubernetes

+ There is an application deployed on Kubernetes cluster. Recently, the Nautilus application development team developed a new version of the application that needs to be deployed now. As per new updates some new changes need to be made in this existing setup. So update the deployment and service as per details mentioned below:
+ We already have a deployment named nginx-deployment and service named nginx-service. Some changes need to be made in this deployment and service, make sure not to delete the deployment and service.
+ Change the service nodeport from 30008 to 32165
+ Change the replicas count from 1 to 5
+ Change the image from nginx:1.18 to nginx:latest

###### Solution:
+ ```shell
  $ kubectl get all
  $ kubectl get svc nginx-service -o yaml > sv.yml #edit the file and changee the port 
  $ kubectl get deployments nginx-deployment -o yaml > dp.yml #edit the fiile and change the rreplicatset and image version  $ kubectl describe svc nginx-service
  $ kubectl describe deployments.apps nginx-deployment 
  $ kubectl apply -f sv.ym
  $ kubectl apply -f dp.yml
  $ kubectl describe svc nginx-service
  $ kubectl describe deployments.apps nginx-deployment
  ```
---
## Replication Controller in Kubernetes

+ The Nautilus DevOps team wants to create a ReplicationController to deploy several pods. They are going to deploy applications on these pods, these applications need highly available infrastructure. Below you can find exact details, create the ReplicationController accordingly.
+ Create a ReplicationController using httpd image, preferably with latest tag, and name it as httpd-replicationcontroller.
+ Labels app should be httpd_app, and labels type should be front-end. The container should be named as httpd-container and also make sure replica counts are 3.
+ All pods should be running state after deployment.

###### Solution:
+ ```yaml
  apiVersion: v1
  kind: ReplicationController
  metadata:
    name: httpd-replicationcontroller
    labels:
      app: httpd_app
      type: front-end
  spec:
    replicas: 3
    selector:
      app: httpd_app
    template:
      metadata:
        labels:
          app: httpd_app
          type: front-end
      spec:
        containers:
        - name: httpd-container
          image: httpd:latest
  ```

---
## Create Replicaset in Kubernetes Cluster

+ The Nautilus DevOps team is going to deploy some applications on kubernetes cluster as they are planning to migrate some of their existing applications there. Recently one of the team members has been assigned a task to write a template as per details mentioned below:
+ Create a ReplicaSet using nginx image with latest tag only and remember to mention tag i.e nginx:latest and name it as nginx-replicaset.
+ Labels app should be nginx_app, labels type should be front-end.
+ The container should be named as nginx-container; also make sure replicas counts are 4.

###### Solution
+ ```yaml
  apiVersion: apps/v1
  kind: ReplicaSet
  metadata:
    name: nginx-replicaset
    labels:
      app: nginx_app
      type: front-end
  spec:
    replicas: 4
    selector:
      matchLabels: 
        type: front-end
    template:
      metadata:
        labels:
          type: front-end
      spec:
        containers:
        - name: nginx-container
          image: nginx:latest
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
## Fix Issue with VolumeMounts in Kubernetes:
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
---
## Kubernetes Shared Volumes

+ We are working on an application that will be deployed on multiple containers within a pod on Kubernetes cluster. There is a requirement to share a volume among the containers to save some temporary data. The Nautilus DevOps team is developing a similar template to replicate the scenario. Below you can find more details about it.
+ Create a pod named volume-share-xfusion.
+ For the first container, use image debian with latest tag only and remember to mention the tag i.e debian:latest, container should be named as volume-container-xfusion-1, and run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/blog.
+ For the second container, use image debian with the latest tag only and remember to mention the tag i.e debian:latest, container should be named as volume-container-xfusion-2, and again run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/apps.
+ Volume name should be volume-share of type emptyDir.
+ After creating the pod, exec into the first container i.e volume-container-xfusion-1, and just for testing create a file blog.txt with any content under the mounted path of first container i.e /tmp/blog.
+ The file blog.txt should be present under the mounted path /tmp/apps on the second container volume-container-xfusion-2 as well, since they are using a shared volume.

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: volume-share-xfusion
  spec:
    volumes:
    - name: volume-share
      emptyDir: {}
    containers:
    - image: debian:latest
      name: volume-container-xfusion-1
      command: ["sleep"]
      args: ["1000"]
      volumeMounts: 
      - name: volume-share
        mountPath: /tmp/blog
    - image: debian:latest
      name: volume-container-xfusion-2
      command:
      - sleep
      args:
      - "1000"
      volumeMounts:
      - name: volume-share
        mountPath: /tmp/apps        
  ```
+ ```shell
  thor@jump_host ~$ kubectl get pods
  NAME                   READY   STATUS    RESTARTS   AGE
  volume-share-xfusion   2/2     Running   0          8m15s
  # list containers within the pod
  thor@jump_host ~$ kubectl get pods volume-share-xfusion -o jsonpath="{.spec['containers'][*].name}"
  volume-container-xfusion-1 volume-container-xfusion-2
  #create blog.txt file within the first container
  thor@jump_host ~$ kubectl exec pods/volume-share-xfusion -c volume-container-xfusion-1 -- touch /tmp/blog/blog.txt
  #verify in container 2
  thor@jump_host ~$ kubectl exec pods/volume-share-xfusion -c volume-container-xfusion-2 -- ls /tmp/apps
  blog.txt
  ```
---
## Kubernetes Sidecar Containers

+ We have a web server container running the nginx image. The  access and error logs generated by the web server are not critical enough to be placed on a persistent volume. However, Nautilus developers need access to the last 24 hours of logs so that they can trace issues and bugs. Therefore, we need to ship the access and error logs for the web server to a log-aggregation service. Following the separation of concerns principle, we implement the Sidecar pattern by deploying a second container that ships the error and access logs from nginx. Nginx does one thing, and it does it well—serving web pages. The second container also specializes in its task—shipping logs. Since containers are running on the same Pod, we can use a shared emptyDir volume to read and write logs.
+ Create a pod named webserver.
+ Create an emptyDir volume shared-logs.
+ Create two containers from nginx and ubuntu images with latest tag only and remember to mention tag i.e nginx:latest, nginx container name should be nginx-container and ubuntu container name should be sidecar-container on webserver pod.
+ Add command on sidecar-container "sh","-c","while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"
+ Mount the volume shared-logs on both containers at location /var/log/nginx, all containers should be up and running.

###### Solution

+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
      name: webserver
  spec:
      volumes:
        - name: shared-logs
          emptyDir: {}
      containers:
      - image: nginx:latest
        name: nginx-container
        volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
      - image: ubuntu:latest 
        name: sidecar-container
        command: ["sh","-c","while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]
        volumeMounts:
        - name: shared-logs
          mountPath: /var/log/nginx
  ```
+ ```sh
  $ kubectl create -f task.yml
  $ watch kubectl get pods
  $ kubectl get pods webserver -o jsonpath="{.spec['containers'][*].name}" 
  ```
---
## Deploy Nginx Web Server on Kubernetes Cluster

+ Some of the Nautilus team developers are developing a static website and they want to deploy it on Kubernetes cluster. They want it to be highly available and scalable. Therefore, based on the requirements, the DevOps team has decided to create a deployment for it with multiple replicas. Below you can find more details about it:
+ Create a deployment using nginx image with latest tag only and remember to mention the tag i.e nginx:latest. Name it as nginx-deployment. The container should be named as nginx-container, also make sure replica counts are 3.
+ Create a NodePort type service named nginx-service. The nodePort should be 30011.

###### Solution
+ ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx-deployment
    labels:
      app: static-app
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: static-app
    template:
      metadata:
        labels:
          app: static-app
      spec:
        containers:
        - name: nginx-container
          image: nginx:latest
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: nginx-service
  spec:
    type: NodePort
    selector:
      app: static-app
    ports:
      - nodePort: 30011
        port: 80
  ```
  ---
## Print Environment Variables
  The Nautilus DevOps team is working on to setup some pre-requisites for an application that will send the greetings to different users. There is a sample deployment, that needs to be tested. Below is a scenario which needs to be configured on Kubernetes cluster. Please find below more details about it.
+ Create a pod named print-envars-greeting.
+ Configure spec as, the container name should be print-env-container and use bash image.
+ Create three environment variables:
  + a. GREETING and its value should be Welcome to
  + b. COMPANY and its value should be xFusionCorp
  + c. GROUP and its value should be Group
  + Use command to echo ["$(GREETING) $(COMPANY) $(GROUP)"] message.
  + You can check the output using kubectl logs -f print-envars-greeting command.

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: print-envars-greeting
  spec:
    containers:
    - name: print-env-container
      image: bash
      command: ["echo"]
      args: ["$(msg)"]
      env:
        - name: GREETING
          value: "Welcome to"
        - name: COMPANY
          value: "xFusionCorp"
        - name: GROUP
          value: "Group"
        - name: msg
          value: "$(GREETING) $(COMPANY) $(GROUP)"
  ```
---
## Rolling Updates And Rolling Back Deployments in Kubernetes

+ There is a production deployment planned for next week. The Nautilus DevOps team wants to test the deployment update and rollback on Dev environment first so that they can identify the risks in advance. Below you can find more details about the plan they want to execute.
+ Create a namespace devops. Create a deployment called httpd-deploy under this new namespace, It should have one container called httpd, use httpd:2.4.25 image and 3 replicas. The deployment should use RollingUpdate strategy with maxSurge=1, and maxUnavailable=2. Also create a NodePort type service named httpd-service and expose the deployment on nodePort: 30008.
+ Now upgrade the deployment to version httpd:2.4.43 using a rolling update.
+ Finally, once all pods are updated undo the recent update and roll back to the previous/original version.

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: devops
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata: 
    name: httpd-deploy
    namespace: devops
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: httpd-deploy
    template:
      metadata:
        labels:
          app: httpd-deploy
      spec:
        containers:
        - name: httpd
          image: httpd:2.4.25
    strategy:
      type: RollingUpdate
      rollingUpdate: 
        maxSurge: 1
        maxUnavailable: 2
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: httpd-service
    namespace: devops
  spec:
    type: NodePort
    selector:
      app: httpd-deploy
    ports:
    - port: 80
      nodePort: 30008
  ```
+ ```shell
  $ kubectl create -f task.yml
  $ kubectl get -n devops deployments.apps
  $ kubectl set image -n devops deployments/httpd-deploy httpd=httpd:2.4.43
  $ kubectl rollout history -n xfusion deployments/httpd-deploy # You should see 1 and 2 versions. which means you are running the 3rd version
  $ k rollout undo -n xfusion deployments/httpd-deploy # now you should see 2 and 3 versions. which means you are running the 1st version
  ```
---
## Deploy Jenkins on Kubernetes Cluster

+ The Nautilus DevOps team is planning to set up a Jenkins CI server to create/manage some deployment pipelines for some of the projects. They want to set up the Jenkins server on Kubernetes cluster. Below you can find more details about the task:
+ Create a namespace jenkins
+ Create a Service for jenkins deployment. Service name should be jenkins-service under jenkins namespace, type should be NodePort, nodePort should be 30008
+ Create a Jenkins Deployment under jenkins namespace, It should be name as jenkins-deployment , labels app should be jenkins , container name should be jenkins-container , use jenkins/jenkins image , containerPort should be 8080 and replicas count should be 1.  

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: jenkins
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: jenkins-service
    namespace: jenkins
  spec:
    type: NodePort
    selector:
      app: jenkins
    ports:
    - port: 8080
      nodePort: 30008
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: jenkins-deployment
    namespace: jenkins
    labels:
      app: jenkins
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: jenkins
    template:
      metadata:
        labels:
          app: jenkins
      spec:
        containers:
        - image: jenkins/jenkins
          name: jenkins-container
          ports:
            - containerPort: 8080
  ```
---
## Deploy Grafana on kubernetes Cluster

The Nautilus DevOps teams is planning to set up a Grafana tool to collect and analyze analytics from some applications. They are planning to deploy it on Kubernetes cluster. Below you can find more details.
+ Create a deployment named grafana-deployment-nautilus using any grafana image for Grafana app. Set other parameters as per your choice.
+ Create NodePort type service with nodePort 32000 to expose the app.

###### Solution

+ ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: grafana-deployment-datacenter
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: grafana
    template:
      metadata:
        labels:
          app: grafana
      spec:
        containers:
        - image: grafana
          name: grafana-container
          containerPort: 3000
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: grafana-service
  spec:
    type: NodePort
    selector:
      app: grafana
    ports:
    - port: 3000
      nodePort: 32000
  ```
---
## Deploy Tomcat App on Kubernetes

A new java-based application is ready to be deployed on a Kubernetes cluster. The development team had a meeting with the DevOps team to share the requirements and application scope. The team is ready to setup an application stack for it under their existing cluster. Below you can find the details for this:
+ Create a namespace named tomcat-namespace-xfusion.
+ Create a deployment for tomcat app which should be named as tomcat-deployment-xfusion under the same namespace you created. Replica count should be 1, the container should be named as tomcat-container-xfusion, its image should be gcr.io/kodekloud/centos-ssh-enabled:tomcat and its container port should be 8080.
+ Create a service for tomcat app which should be named as tomcat-service-xfusion under the same namespace you created. Service type should be NodePort and nodePort should be 32227.

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: tomcat-namespace-xfusion
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: tomcat-deployment-xfusion
    namespace: tomcat-namespace-xfusion
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: tomcat-xfusion
    template:
      metadata:
        labels:
          app: tomcat-xfusion
      spec:
        containers:
        - image: gcr.io/kodekloud/centos-ssh-enabled:tomcat
          name: tomcat-container-xfusion
          ports:
          - containerPort: 8080
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: tomcat-service-xfusion
    namespace: tomcat-namespace-xfusion
  spec:
    type: NodePort
    selector:
      app: tomcat-xfusion
    ports:
    - port: 8080
      nodePort: 32227
  ```
---
## Deploy Node App on Kubernetes 

The Nautilus development team has completed development of one of the node applications, which they are planning to deploy on a Kubernetes cluster. They recently had a meeting with the DevOps team to share their requirements. Based on that, the DevOps team has listed out the exact requirements to deploy the app. Find below more details:
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
## Troubleshoot Deployment issues in Kubernetes

Last week, the Nautilus DevOps team deployed a redis app on Kubernetes cluster, which was working fine so far. This morning one of the team members was making some changes in this existing setup, but he made some mistakes and the app went down. We need to fix this as soon as possible. Please take a look.
+ The deployment name is redis-deployment. The pods are not in running state right now, so please look into the issue and fix the same.

+ ```yaml
  #using the command `k get events` I found that kubernetes is crashing because of the following errors:
  spec:
    containers:
    - image: redis:alpin #it should be alpine not alpine
      imagePullPolicy: IfNotPresent
      name: redis-container
    volumes:
    - configMap:
        defaultMode: 420
        name: redis-conig #it should be redis-config, use the command `k get cm`.
      name: config       
  ```
## Fix issue with LAMP Environment in Kubernetes

One of the DevOps team member was trying to install a WordPress website on a LAMP stack which is essentially deployed on Kubernetes cluster. It was working well and we could see the installation page a few hours ago. However something is messed up with the stack now due to a website went down. Please look into the issue and fix it:
+ FYI, deployment name is lamp-wp and its using a service named lamp-service. The Apache is using http default port and nodeport is 30008. From the application logs it has been identified that application is facing some issues while connecting to the database in addition to other issues. Additionally, there are some environment variables associated with the pods like MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD, MYSQL_HOST.

###### Solution
To make the trouble shooting process easier, we will create aliases for the following commands:
+ ```shell
  HTTP=$(kubectl get pod -o jsonpath='{.items[*].spec.containers[0].name}') ; echo "HTTP: $HTTP" # HTTP: httpd-php-container
  MYSQL=$(kubectl get pod -o jsonpath='{.items[*].spec.containers[1].name}') ; echo "MYSQL: $MYSQL" # MYSQL: mysql-container0
  POD=$(kubectl get pod -o jsonpath='{.items[*].metadata.name)}') ; echo "POD: $POD" # POD: lamp-wp-56c7c454fc-cq8c2
  ```
Okey, we can list our objects and inspect them.
+ ```shell
  $ kubectl get pods,svc,cm,secrets,deployments
  NAME                           READY   STATUS    RESTARTS   AGE
  pod/lamp-wp-56c7c454fc-cq8c2   2/2     Running   0          45s

  NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
  deployment.apps/lamp-wp   1/1     1            1           45s

  NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
  service/kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP        2m59s
  service/lamp-service    NodePort    10.96.240.157   <none>        80:30009/TCP   45s
  service/mysql-service   ClusterIP   10.96.254.52    <none>        3306/TCP       45s

  NAME                     TYPE     DATA   AGE
  secret/mysql-db-url      Opaque   1      47s
  secret/mysql-host        Opaque   1      47s
  secret/mysql-root-pass   Opaque   1      48s
  secret/mysql-user-pass   Opaque   2      47s
  ```
You notice that the service is exposed at nodePort 30009 instead of 30008 which is already mentioned in the task description. To fix this we can edit the service.
+ ```shell
  $ kubectl get svc lam-service -o yaml > fix.yml
  $ sed -i "s/30009/30008/g" fix.yml
  $ kubectl apply -f fix.yml
  ```
After checking the logs using `kubectl logs -f $POD -c $HTTP` it was shown that there was an incorrect variable. The deployment definiton was safe and following the instructions. Let's look inside the container HTTP itself.
+ ```shell
  $ kubectl exec -it $POD -c $HTTP -- sh
  $ cat app/index.php
  <?php
  $dbname = $_ENV['MYSQL_DATABASE'];
  $dbuser = $_ENV['MYSQL_USER'];
  $dbpass = $_ENV['MYSQL_PASSWORDS']; #fix to ['MYSQL_PASSWORD']
  $dbhost = $_ENV['HOST_MYQSL']; #fix to ['MYSQL_HOST"]
  
  $connect = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");
  $test_query = "SHOW TABLES FROM $dbname";
  $result = mysqli_query($test_query);

  if ($result->connect_error) {
    die("Connection failed: " . $conn->connect_error);
  }
    echo "Connected successfully";
  ```
HERE, we caught the TYPOOO!! $dbhost && $dbpass, lets fix them and restart the php-fpm server!
+ ```shell
  $ service php-fpm restart
  $ service php-fpm status
  ```
A quick check on the vars on both containers!
+ ```shell
  $ kubectl exec -it $POD -c $HTTP -- env | grep -i MYSQL
  $ kubectl exec -it $POD -c $MYSQL -- env | grep -i MYSQL
  $ kubectl describe pod | grep -A 6 ENV
  ```
> Click on the *App* button at the top right to open the app URL in a new tab, notice the msh "connected successfully".
---
## Deploy Lamp Stack on Kubernetes Cluster

The Nautilus DevOps team want to deploy a PHP website on Kubernetes cluster. They are going to use Apache as a web server and Mysql for database. The team had already gathered the requirements and now they want to make this website live. Below you can find more details:
+ Create a config map php-config for php.ini with variables_order = "EGPCS" data.
+ Create a deployment named lamp-wp.
+ Create two containers under it. First container must be httpd-php-container using image webdevops/php-apache:alpine-3-php7 and second container must be mysql-container from image mysql:5.6. Mount php-config configmap in httpd container at /opt/docker/etc/php/php.ini location.
+ Create kubernetes generic secrets for mysql related values like myql root password, mysql user, mysql password, mysql host and mysql database. Set any values of your choice.
+ Add some environment variables for both containers:
+ MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD and MYSQL_HOST. Take their values from the secrets you created. Please make sure to use env field (do not use envFrom) to define the name-value pair of environment variables.
+ Create a node port type service lamp-service to expose the web application, nodePort must be 30008.
+ Create a service for mysql named mysql-service and its port must be 3306.
+ We already have /tmp/index.php file on jump_host server.
+ Copy this file into httpd container under Apache document root i.e /app and replace the dummy values for mysql related variables with the environment variables you have set for mysql related parameters. Please make sure you do not hard code the mysql related details in this file, you must use the environment variables to fetch those values.
+ You must be able to access this index.php on node port 30008 at the end, please note that you should see Connected successfully message while accessing this page.

###### Solution:

+ ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: php-config
  data:
    php.ini: |
      variables_order="EGPCS"

  ---

  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: lamp-wp
    labels:
      app: php-mysql-dep
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: php-mysql-dep
    template:
      metadata:
        labels:
          app: php-mysql-dep
      spec:
        containers:
        - name: httpd-php-container
          image: webdevops/php-apache:alpine-3-php7
          ports:
          - containerPort: 80
          env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: rootpassword
          - name: MYSQL_DATABASE
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: database
          - name: MYSQL_USER
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: user
          - name: MYSQL_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: password
          - name: MYSQL_HOST
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: host
          volumeMounts:
          - mountPath: /opt/docker/etc/php/php.ini
            name: php-config
            subPath: php.ini
        - name: mysql-container
          image: mysql:5.6
          ports:
          - containerPort: 3306
          env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: rootpassword
          - name: MYSQL_DATABASE
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: database
          - name: MYSQL_USER
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: user
          - name: MYSQL_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: password
          - name: MYSQL_HOST
            valueFrom:
              secretKeyRef:
                name: mysql-secrets
                key: host
        volumes:
        - name: php-config
          configMap:
            name: php-config

  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: mysql-secrets
  type: Opaque
  data:
    rootpassword: cm9vdHBhc3N3b3Jk
    database: bXlkYg==
    user: Y2h4bXhpaQ==
    password: Y2h4bXhpaXBhc3N3b3Jk
    host: bXlzcWwtc2VydmljZQ==
  ---

  apiVersion: v1
  kind: Service
  metadata:
    name: lamp-service
  spec:
    type: NodePort
    ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
    selector:
      app: php-mysql-dep

  ---

  apiVersion: v1 
  kind: Service
  metadata:
    name: mysql-service
  spec:
    ports:
    - port: 3306
    selector:
      app: php-mysql-dep
  ```
+ ```sh
  $ kubectl get pods
  $ HTTP=$(kubectl get pod  -o=jsonpath='{.items[*].spec.containers[0].name}')
  $ POD=$(kubectl get pod  -o=jsonpath='{.items[*].metadata.name}')
  $ kubectl cp /tmp/index.php $POD:/app -c $HTTP
  $ kubectl exec -it $POD -c $HTTP -- bash
  $ vi /app/index.php
  ```
+ ```php
  <?php
  $dbname = $_ENV["MYSQL_DATABASE"];
  $dbuser = $_ENV["MYSQL_USER"];
  $dbpass = $_ENV["MYSQL_PASSWORD"];
  $dbhost = $_ENV["MYSQL_HOST"];

  $connect = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");

  $test_query = "SHOW TABLES FROM $dbname";
  $result = mysqli_query($test_query);

  if ($result->connect_error) {
    die("Connection failed: " . $conn->connect_error);
  }
    echo "Connected successfully";
  ```
---
## Init Containers in Kubernetes

There are some applications that need to be deployed on Kubernetes cluster and these apps have some pre-requisites where some configurations need to be changed before deploying the app container. Some of these changes cannot be made inside the images so the DevOps team has come up with a solution to use init containers to perform these tasks during deployment. Below is a sample scenario that the team is going to test first.
+ Create a Deployment named as ic-deploy-datacenter.
+ Configure spec as replicas should be 1, labels app should be ic-datacenter, template's metadata lables app should be the same ic-datacenter.
+ The initContainers should be named as ic-msg-datacenter, use image fedora, preferably with latest tag and use command '/bin/bash', '-c' and 'echo Init Done - Welcome to xFusionCorp Industries > /ic/news'. The volume mount should be named as ic-volume-datacenter and mount path should be /ic.
+ Main container should be named as ic-main-datacenter, use image fedora, preferably with latest tag and use command '/bin/bash', '-c' and 'while true; do cat /ic/news; sleep 5; done'. The volume mount should be named as ic-volume-datacenter and mount path should be /ic.
+ Volume to be named as ic-volume-datacenter and it should be an emptyDir type. 

###### Solution:
+ ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ic-deploy-datacenter
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: ic-datacenter
    template:
      metadata:
        labels:
          app: ic-datacenter
      spec:
        initContainers: 
        - name: ic-msg-datacenter
          image: fedora:latest
          command: ['/bin/bash', '-c', 'echo Init Done - Welcome to xFusioncorp Industries > /ic/news']
          volumeMounts:
          - name: ic-volume-datacenter
            mountPath: /ic
        containers:
        - name: ic-main-datacenter
          image: fedora:latest
          command: ['/bin/bash', '-c', 'while true; do cat /ic/news; sleep 5; done']
          volumeMounts:
          - name: ic-volume-datacenter
            mountPath: /ic 
        volumes:
        - name: ic-volume-datacenter
          emptyDir: {}
  ```
---
## Persistent Volumes in Kubernetes

+ The Nautilus DevOps team is working on a Kubernetes template to deploy a web application on the cluster. There are some requirements to create/use persistent volumes to store the application code, and the template needs to be designed accordingly. Please find more details below:
+ Create a PersistentVolume named as pv-xfusion. Configure the spec as storage class should be manual, set capacity to 3Gi, set access mode to ReadWriteOnce, volume type should be hostPath and set path to /mnt/dba (this directory is already created, you might not be able to access it directly, so you need not to worry about it).
+ Create a PersistentVolumeClaim named as pvc-xfusion. Configure the spec as storage class should be manual, request 1Gi of the storage, set access mode to ReadWriteOnce.
+ Create a pod named as pod-xfusion, mount the persistent volume you created with claim name pvc-xfusion at document root of the web server, the container within the pod should be named as container-xfusion using image nginx with latest tag only (remember to mention the tag i.e nginx:latest).
+ Create a node port type service named web-xfusion using node port 30008 to expose the web server running within the pod.

###### Solution:
+ ```yaml
  apiVersion: v1
  kind: PersistentVolume
  metadata: 
    name: pv-xfusion
  spec:
    storageClassName: manual
    capacity:
      storage: 3Gi
    accessModes:
      - ReadWriteOnce
    hostPath: 
      path: /mnt/dba 
  ---
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata: 
    name: pvc-xfusion
  spec:
    storageClassName: manual
    resources:
      requests: 
        storage: 1Gi
    accessModes:
      - ReadWriteOnce
  ---
  apiVersion: v1
  kind: Pod
  metadata: 
    name: pod-xfusion
    labels:
      app: webapp
  spec:
    volumes:
      - name: web-server-volume
        persistentVolumeClaim:
          claimName: pvc-xfusion
    containers:
    - name: container-xfusion
      image: nginx:latest
      ports:
      - containerPort: 80
      volumeMounts:
      - mountPath: "/var/www/html"
        name: web-server-volume
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: web-xfusion
  spec:
    type: NodePort
    ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
    selector:
      app: webapp
  ```
---
## Manage Secrets in Kubernetes

+ The Nautilus DevOps team is working to deploy some tools in Kubernetes cluster. Some of the tools are licence based so that licence information needs to be stored securely within Kubernetes cluster. Therefore, the team wants to utilize Kubernetes secrets to store those secrets. Below you can find more details about the requirements:

+ We already have a secret key file media.txt under /opt location on jump host. Create a generic secret named media, it should contain the password/license-number present in media.txt file.
+ Also create a pod named secret-xfusion.
+ Configure pod's spec as container name should be secret-container-xfusion, image should be fedora preferably with latest tag (remember to mention the tag with image). Use sleep command for container so that it remains in running state. Consume the created secret and mount it under /opt/games within the container.
+ To verify you can exec into the container secret-container-xfusion, to check the secret key under the mounted path /opt/games.

###### Solution:
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: secret-xfusion
  spec:
    containers:
    - name: secret-container-xfusion
      image: fedora:latest
      command: ['/bin/bash', '-c','sleep 200']
      volumeMounts:
      - name: secret-licence
        mountPath: "/opt/games"
        readOnly: true
    volumes: 
    - name: secret-licence 
      secret:
        secretName: media
  ```
+ ```shell
  $ kubectl create secret generic media --from-file=/opt/media.txt
  $ kubectl create -f pod.yml
  $ kubectl get secrets,pods
  $ kubectl exec -it secret-xfusion -c secret-container-xfusion -- ls /opt/games
  ```
---
## Environment Variables in Kubernetes

There are a number of parameters that are used by the applications. We need to define these as environment variables, so that we can use them as needed within different configs. Below is a scenario which needs to be configured on Kubernetes cluster. Please find below more details about the same.

+ Create a pod named envars.
+ Container name should be fieldref-container, use image httpd preferable latest tag, use command `sh`, `-c` and *args* should be
`'while true; do echo -en '/n'; printenv NODE_NAME POD_NAME; printenv POD_IP POD_SERVICE_ACCOUNT; sleep 10; done;'`

+ Define Four environment variables as mentioned below:
  + The first env should be named as NODE_NAME, set valueFrom fieldref and fieldPath should be spec.nodeName.
  + The second env should be named as POD_NAME, set valueFrom fieldref and fieldPath should be metadata.name.
  + The third env should be named as POD_IP, set valueFrom fieldref and fieldPath should be status.podIP.
  + The fourth env should be named as POD_SERVICE_ACCOUNT, set valueFrom fieldref and fieldPath shoulbe be spec.serviceAccountName.
  + Set restart policy to Never.
  + To check the output, exec into the pod and use `printenv` command.

###### Solution
+ ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: envars
  spec:
    containers:
    - name: fieldref-container
      image: httpd:latest
      command: ['sh','-c']
      args: 
      - while true; do 
          echo -en '/n'; 
          printenv NODE_NAME POD_NAME; 
          printenv POD_IP POD_SERVICE_ACCOUNT; 
          sleep 10; 
        done;
      env:
      - name: NODE_NAME
        valueFrom:
          fieldRef:
            fieldPath: spec.nodeName
      - name: POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
      - name: POD_SERVICE_ACCOUNT
        valueFrom: 
          fieldRef:
            fieldPath: spec.serviceAccountName
    restartPolicy: Never
  ```
Run the following command to verify `kubectl exec -it pods/envars -c fieldref-container -- bash`
---
## Kubernetes LEMP Setup
The Nautilus DevOps team want to deploy a static website on Kubernetes cluster. They are going to use Nginx, phpfpm and MySQL for the database. The team had already gathered the requirements and now they want to make this website live. Below you can find more details:

Create some secrets for MySQL.
+ Create a secret named mysql-root-pass wih key/value pairs as below:
  >name: password
  value: R00t

+ Create a secret named mysql-user-pass with key/value pairs as below:
>name: username
value: kodekloud_rin
name: password
value: TmPcZjtRQx

+ Create a secret named mysql-db-url with key/value pairs as below:
  >name: database
  value: kodekloud_db7

+ Create a secret named mysql-host with key/value pairs as below:
  >name: host
  value: mysql-service

+ Create a config map php-config for php.ini with variables_order = "EGPCS" data.
+ Create a deployment named lemp-wp.
+ Create two containers under it. First container must be nginx-php-container using image webdevops/php-nginx:alpine-3-php7 and second container must be mysql-container from image mysql:5.6. Mount php-config configmap in nginx container at /opt/docker/etc/php/php.ini location.
1) Add some environment variables for both containers:
> MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD and MYSQL_HOST. Take their values from the secrets you created. Please make sure to use env field (do not use envFrom) to define the name-value pair of environment variables.

+ Create a node port type service lemp-service to expose the web application, nodePort must be 30008.
+ Create a service for mysql named mysql-service and its port must be 3306.
+ We already have a /tmp/index.php file on jump_host server.
+ Copy this file into the nginx container under document root i.e /app and replace the dummy values for mysql related variables with the environment variables you have set for mysql related parameters. Please make sure you do not hard code the mysql related details in this file, you must use the environment variables to fetch those values.
+ Once done, you must be able to access this website using Website button on the top bar, please note that you should see Connected successfully message while accessing this page.

###### Solution:
+ ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: php-config
  data:
    php.ini: |
      variables_order="EGPCS"
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: lemp-wp
    labels:
      app: php-mysql-dep
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: php-mysql-dep
    template:
      metadata:
        labels:
          app: php-mysql-dep
      spec:
        containers:
        - name: nginx-php-container 
          image: webdevops/php-nginx:alpine-3-php7
          ports:
          - containerPort: 80
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-root-pass
                  key: password
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: username
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-db-url
                  key: database
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: password
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-host
                  key: host
          volumeMounts:
          - mountPath: /opt/docker/etc/php/php.ini
            name: php-config
            subPath: php.ini
        - name: mysql-container
          image: mysql:5.6
          ports:
          - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-root-pass
                  key: password
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: username
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-db-url
                  key: database
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-user-pass
                  key: password
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-host
                  key: host
        volumes:
        - name: php-config
          configMap:
            name: php-config
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: lemp-service
  spec:
    type: NodePort
    ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
    selector:
      app: php-mysql-dep
  ---
  apiVersion: v1 
  kind: Service
  metadata:
    name: mysql-service
  spec:
    ports:
    - port: 3306
    selector:
      app: php-mysql-dep
  ```

+ ```shell
  kubectl create secret generic mysql-root-pass --from-literal=password=r00t \
  && kubectl create secret generic mysql-user-pass --from-literal=username=kodekloud_rin --from-literal=password=TmPcZjtRQx \
  && kubectl create secret generic mysql-db-url --from-literal=database=kodekloud_db7 \
  && kubectl create secret generic mysql-host --from-literal=host=mysql-service \
  ```
---
## Kubernetes Troubleshooting

One of the Nautilus DevOps team members was working on to update an existing Kubernetes template. Somehow, he made some mistakes in the template and it is failing while applying. We need to fix this as soon as possible, so take a look into it and make sure you are able to apply it without any issues. Also, do not remove any component from the template like pods/deployments/volumes etc.

+ /home/thor/mysql_deployment.yml is the template that needs to be fixed.

###### Solution;
+ ```yaml
  apiVersion: v1 
  kind: PersistentVolume
  metadata:
    name: mysql-pv
    labels:
      type: local
  spec:
    storageClassName: standard
    capacity:
      storage: 250Mi
    accessModes: ['ReadWriteOnce']
    hostPath:
      path: "/mnt/data"
    persistentVolumeReclaimPolicy: Retain
  ---
  apiVersion: v1 
  kind: PersistentVolumeClaim
  metadata:
    name: mysql-pv-claim
    labels:
      app: mysql-app 
  spec:
    storageClassName: standard
    accessModes: ['ReadWriteOnce']
    resources:
      requests:
        storage: 250Mi
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: mysql
    labels:
      app: mysql-app  
  spec:
    type: NodePort
    ports:
      - targetPort: 3306
        port: 3306
        nodePort: 30011
    selector:
      app: mysql_app
      tier: mysql
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: mysql-deployment
    labels:
      app: mysql-app
  spec:
    strategy:
      type: Recreate
    selector:
      matchLabels:
        app: mysql-app
        tier: mysql
    template:
      metadata:
        labels:
          app: mysql-app
          tier: mysql
      spec:
        containers:
        - image: mysql:5.6
          name: mysql
          env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-root-pass
                key: password
          - name: MYSQL_DATABASE
            valueFrom:
              secretKeyRef:
                name: mysql-db-url
                key: database
          - name: MYSQL_USER
            valueFrom:
              secretKeyRef:
                name: mysql-user-pass
                key: username
          - name: MYSQL_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysql-user-pass
                key: password
          ports:
          - containerPort: 3306
          volumeMounts:
          - name: mysql-persistent-storage
            mountPath: /var/lib/mysql
        volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
  ```