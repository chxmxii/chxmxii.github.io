---
author:
  name: "chxmxii_"
date: 2022-09-10
tags:
  - k8s
  - nginx
  - DevOps
linktitle: 
type:
- post
- posts
title: Kubernetes Nginx Web Server Deployement 
weight: 10
series:
- Hugo 101
---

1 - Create nginx-deployement.yaml file :

```yaml
apiVersion : apps/v1
kind: Deployment
metadata:
	name: nginx
	labels:
		app: nginx
spec:
	replicas: 1
	selector:
		matchlabels:
			app: nginx
	template: 
		metadata: 
			labels:
				app: nginx
		spec:
			containers:
			- name: nginx
				image: nginx
				ports:
				- containerPort: 80
```

2 - Create the deployment : 
`kubectl create -f dep.yaml`
   ⇒ This will create one pod with single NGINX container listening on port 80.

3 - Verify :

`Kubectl get deployment`

4 - Create nginx-service.yaml : 

```yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-service
spec:
	selector:
		app: nginx
	type: NodePort
	ports:
	- protocol: TCP
		port: 80
		targetpPort: 80
```

5 - Create the service : 

`kubectl create -f nginx-service.yaml`

⇒ Service will be created as a nodePort, means it will expose the nginx web-server on each node with port 80. pods are selected for this service depending on label selector”app:nginx”, same label we specified while creating the nginx pode in dep.

6 - verify :

`kubectl get svc`