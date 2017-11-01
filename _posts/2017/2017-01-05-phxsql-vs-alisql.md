---
layout: post
title: PhxSQL vs AliSQL
date: 2017-01-05 18:28:58
categories:
- opensource
tags:
---

PhxSQL是一个兼容MySQL、服务高可用、数据强一致的关系型数据库集群。PhxSQL以单Master多Slave方式部署，在集群内超过一半机器存活的情况下、即可提供服务，并且自身实现自动Master切换、保证数据一致性。PhxSQL不依赖于Zookeeper等任何第三方做存活检测及选主。PhxSQL基于MySQL的一个分支Percona 5.6开发，功能和实现与MySQL基本一致。  

[微信开源PhxSQL背后：强一致高可用分布式数据库的设计和实现哲学](http://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2650994184&idx=1&sn=9be9eb8ab569ad281330b6ceeb490757&chksm=bdbf0e5b8ac8874d3407d84eed6175e4b82b0202fc641ec4d52bc001c9497fe8faa0706224cf&scene=21#wechat_redirect)

AliSQL是基于MySQL官方版本的一个分支，由阿里云数据库团队维护，目前也应用于阿里巴巴集团业务以及阿里云数据库服务。该版本在社区版的基础上做了大量的性能与功能的优化改进。尤其适合电商、云计算以及金融等行业环境。该版本性能优于社区版MySQL 70%左右，可帮助中小企业和开发者提升数据运营能力。

[专访丁奇：阿里云即将开源AliSQL，超大并发、针对秒杀优化](http://www.infoq.com/cn/news/2016/09/AliSQL-ali-cloud-AliSQL)