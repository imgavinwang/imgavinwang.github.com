---
layout: post
title: ubuntu下mysql-python模块的安装
date: 2015-05-08 18:28:58
categories:
- ubuntu
- python
- mysql
tags:

---

各种方法各种报错，终于找到如下步骤，简单搞定，记录下。  

```
sudo apt-get install python-setuptools
sudo apt-get install libmysqld-dev
sudo apt-get install libmysqlclient-dev
sudo apt-get install python-dev
sudo easy_install mysql-python
```

```
##mysql for python yum##
yum install python-pip
yum install python-devel mysql-devel zlib-devel openssl-devel
pip install mysql-python
```