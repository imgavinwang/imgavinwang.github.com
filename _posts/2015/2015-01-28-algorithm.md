---
layout: post
title: 服务器常用算法
date: 2015-01-28 18:28:58
categories:
- algorithm
tags:
---

排序（选择selectionSort、冒泡bubbleSort、插入insertSort、希尔shellSort(分组插入)、快速quickSort、堆heapSort、归并mergeSort、基数radixSort、桶bucketSort、计数countingSort，二叉树binaryTreeSort） 时间复杂度 递归
数据结构 链表（单链表，双向链表） 树（二叉树，二叉查找树BST，自平衡二叉树AVL、自平衡二叉树-红黑树RBT、堆heap、小根堆、大根堆） 图（有向图，无向图）

B树和B+广泛应用于文件存储系统以及数据库系统中,B/B+树也经常用做数据库的索引

http://blog.csdn.net/bingjing12345/article/details/7830474
二叉查找树(BST)，平衡二叉查找树(AVL)，红黑树(RBT)，B~/B+树(B-tree）的比较
二叉查找树 (Binary Search Tree)  完全是因为静态查找结构在动态插入，删除结点所表现出来的无能为力。查找最好时间复杂度O(logN)，最坏时间复杂度O(N)。

平衡二叉查找树AVL 查找的时间复杂度维持在O(logN)    ，严格平衡策略以牺牲建立查找结构(插入，删除操作)的代价

红黑树 (Red-Black Tree ) 查找 效率最好情况下时间复杂度为O(logN)，但在最坏情况下比AVL要差一些，但也远远好于BST。


**一致性哈希算法**

在分布式集群中，对机器的添加删除，或者机器故障后自动脱离集群这些操作是分布式集群管理最基本的功能。如果采用常用的hash(object)%N算法，那么在有机器添加或者删除后，很多原有的数据就无法找到了，这样严重的违反了单调性原则。接下来主要讲解一下一致性哈希算法是如何设计的。
http://blog.csdn.net/cywosp/article/details/23397179/

在做服务器负载均衡时候可供选择的负载均衡的算法有很多，包括： 轮循算法(Round Robin)、哈希算法(HASH)、最少连接算法(Least Connection)、响应速度算法(Response Time)、加权法(Weighted )等。其中哈希算法是最为常用的算法.


**base64**

Base64要求把每三个8Bit的字节转换为四个6Bit的字节（3*8 = 4*6 = 24），然后把6Bit再添两位高位0，组成四个8Bit的字节，也就是说，转换后的字符串理论上将要比原来的长1/3。


**RLE压缩算法**

[http://blog.csdn.net/orbit/article/details/7062218](http://blog.csdn.net/orbit/article/details/7062218)

RLE（Run Length Encoding）行程长度压缩算法（也称游程长度压缩算法），是最早出现、也是最简单的无损数据压缩算法。RLE算法的基本思路是把数据按照线性序列分成两种情况：一种是连续的重复数据块，另一种是连续的不重复数据块。对于第一种情况，对连续的重复数据块进行压缩，压缩方法就是用一个表示块数的属性加上一个数据块代表原来连续的若干块数据。对于第二种情况，RLE算法有两种处理方法，一种处理方法是用和第一种情况一样的方法处理连续的不重复数据块，仅仅是表示块数的属性总是1；另一种处理方法是不对数据进行任何处理，直接将原始数据作为压缩后的数据。
