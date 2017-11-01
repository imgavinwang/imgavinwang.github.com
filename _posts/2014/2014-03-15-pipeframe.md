---
layout: post
title: 数据管道
date: 2014-03-15 18:28:58
categories:
- network
tags:
---

数据管道:
- socket
- table
- file
- queue/mq


##1.socket##

A通常为客户端，B为服务器。  
A  ->   socket    -> B  
|------package------>|  


##2.table##

A通常为上游进程，B为下游进程。  
A  ->    table    -> B  
|------records------>|


##3.file##

A通常为上游进程，B为下游进程。  
A  ->    file    -> B


##4.queue/mq 消息队列##

A通常为上游进程，B为下游进程。  
A  ->    queue    -> B
