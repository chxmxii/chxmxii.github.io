---
author:
  name: "chxmxii_"
date: 2021-08-19
linktitle: 
tags:
 - web
 - python
type:
- post
- posts
title: Bypassing common jinja2 filters
weight: 10
series:
- Hugo 101
---

## What is SSTI?
SSTI is an injection vulnerability, similar to other injection vulnerabilities it occurs when unsanitized user input is directly processed by the application or more specifically the template engine. 

Depending on the template engine being used the impact can vary, some of the issues range from:
+ Data exposure (application secrets)
+ Cross Site Scripting
+ Remote Code execution

### String filters 