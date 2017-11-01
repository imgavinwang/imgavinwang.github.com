---
layout: post
title: 最短路径Dijkstra算法和A*算法
date: 2015-04-30 18:28:58
categories:
- algorithm
tags:
---

Dijkstra算法和A*算法都是最短路径问题的常用算法，下面就对这两种算法的特点进行一下比较。  
1.Dijkstra算法计算源点到其他所有点的最短路径长度，A*关注点到点的最短路径(包括具体路径)。  
2.Dijkstra算法建立在较为抽象的图论层面，A*算法可以更轻松地用在诸如游戏地图寻路中。  
3.Dijkstra算法的实质是广度优先搜索，是一种发散式的搜索，所以空间复杂度和时间复杂度都比较高。对路径上的当前点，A*算法不但记录其到源点的代价，还计算当前点到目标点的期望代价，是一种启发式算法，也可以认为是一种深度优先的算法。
4.由第一点，当目标点很多时，A*算法会带入大量重复数据和复杂的估价函数，所以如果不要求获得具体路径而只比较路径长度时，Dijkstra算法会成为更好的选择。  

[Dijkstra算法](http://www.cnblogs.com/biyeymyhjob/archive/2012/07/31/2615833.html)

![戴克斯特拉算法演示](http://upload.wikimedia.org/wikipedia/commons/thumb/5/57/Dijkstra_Animation.gif/220px-Dijkstra_Animation.gif)