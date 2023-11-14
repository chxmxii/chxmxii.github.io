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
+ Make sure all pods are up and running after the update.

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
      volumeMounts: 
      - name: volume-share
        mountPath: /tmp/blog
    - image: debian:latest
      name: volume-container-xfusion-2
      command:
      - sleep
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