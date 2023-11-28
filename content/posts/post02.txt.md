---
author:
  name: "chxmxii_"
date: 2023-11-23
tags:
  - k8s
  - nginx
linktitle: 
type:
- post
- posts
title: Boost Your App's Security with this open-source tool.
weight: 10
series:
- Hugo 101
---
---
Ever wondered how to make your app more secure without diving into complex tech jargon? Meet CrowdSec, an open-source tool that's here to simplify things for you. In this blog, I'll take you through a beginner-friendly lab to enhance your app's security. No need to be a tech guru – let's make your app safer together!  

</
![](/files/crwlogo.png#center)/>
---
---
#### CrowdSec – What is it and how does it work?
So, you've heard about CrowdSec, and it sounds kinda cool, right? Well, it is! Let's break it down without getting too techy.

CrowdSec is like your app's superhero, but it's not just any hero—it's free and open-source. Think of it as this really smart engine that watches out for bad stuff happening to your app. It works super fast, like x60 faster than other tools (because it is written in GO), and it's all about spotting tricky behaviors.

Now, let's talk logs. You know, those things your app spits out with a bunch of info that's hard to make sense of? CrowdSec comes to the rescue by making these logs easier to understand using grok patterns. It's like turning messy handwriting into a clear note.

CrowdSec also has these cool things called scenarios. They're like scripts that tell CrowdSec what to look for, things like DDoS attacks or someone trying to sneak in. When these scenarios happen, CrowdSec has its buddies called bouncers, and they step in to protect your app. Teamwork, right?

Oh, and there's an API thingy, which is like the brain of the operation. It keeps everything running smoothly.

But what's really awesome is that CrowdSec isn't just about you. It's part of this big community—over 150 countries big! People share info about bad IPs and the types of attacks they faced. So, How cool is that?

#### What does DDoS Mean?
![](/files/ddos.gif#center)
---

#### Architecture
![](/files/crwsec.png#center)

#### Deployment
Okey, so..
```yaml
  version: '3'

  services:
    #the application itself : static html served by apache2.
    #the html can be found in ./app/
    app:
      image: httpd:alpine
      restart: always
      volumes:
        - ./app/:/usr/local/apache2/htdocs/
      networks:
        crowdsec_test:
          ipv4_address: 172.20.0.2

    #the reverse proxy that will serve the application
    #you can see nginx's config in ./reverse-proxy/nginx.conf
    reverse-proxy:
      image: nginx:alpine
      restart: always
      ports:
        - 8000:80
      depends_on:
        - 'app'
      volumes:
        - ./reverse-proxy/nginx.conf:/etc/nginx/nginx.conf
        - logs:/var/log/nginx
      networks:
        crowdsec_test:
          ipv4_address: 172.20.0.3
    
    #crowdsec : it will be fed nginx's logs
    #and later we're going to plug a firewall bouncer to it
    crowdsec:
      image: crowdsecurity/crowdsec
      restart: always
      environment:
        #this is the list of collections we want to install
        #https://hub.crowdsec.net/author/crowdsecurity/collections/nginx
        COLLECTIONS: "crowdsecurity/nginx"
        GID: "${GID-1000}"
      depends_on:
        - 'reverse-proxy'
      volumes:
        - ./crowdsec/acquis.yaml:/etc/crowdsec/acquis.yaml
        - logs:/var/log/nginx
        - crowdsec-db:/var/lib/crowdsec/data/
        - crowdsec-config:/etc/crowdsec/
      networks:
        crowdsec_test:
          ipv4_address: 172.20.0.4
    
    #metabase, because security is cool, but dashboards are cooler
    dashboard:
      #we're using a custom Dockerfile so that metabase pops with pre-configured dashboards
      build: ./crowdsec/dashboard
      restart: always
      ports:
        - 3000:3000
      environment:
        MB_DB_FILE: /data/metabase.db
        MGID: "${GID-1000}"
      depends_on:
        - 'crowdsec'
      volumes:
        - crowdsec-db:/metabase-data/
      networks:
        crowdsec_test:
          ipv4_address: 172.20.0.5

  volumes:
    logs:
    crowdsec-db:
    crowdsec-config:

  networks:
    crowdsec_test:
      ipam:
        driver: default
        config:
          - subnet: 172.20.0.0/24
```

[TO BE CONTINUED]...

[`$`] 
