---
layout: post
title: express nodejs
date: 2015-07-06 20:20:20
categories:
- nodejs
tags:
---

```
ubuntu nodejs setup :
apt-get update
apt-get install -y python-software-properties software-properties-common
add-apt-repository ppa:chris-lea/node.js
apt-get update
apt-get install nodejs
export NODE_PATH= "/usr/lib/node_modules"

ubuntu express setup:
npm install express -g
npm install -g express-generator

expres example:
express myapp
cd myapp
npm install jade -g
npm start
```