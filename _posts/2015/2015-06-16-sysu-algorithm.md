---
layout: post
title: 算法分析与设计-张治国-课题笔记
date: 2015-06-16 18:28:58
categories:
- sysu
- algorithm
tags:
---

computation theory
programming theory
algorithm theory
architecture theory

http://pan.baidu.com/s/1kTMLKjH


时间效率 空间效率 复杂度

可重构计算方式     编译器把程序编译成硬件的逻辑线路netlist
CPU 硬件实现 软件实现

按算法策略来划分算法。不是按照问题领域典型划分。

插入和快速排序算法。

算法分析：好坏程度goodness  难易程序hardness
资源：CPU时间 存储空间
算法分析基本假设 依赖数据模块，RAM模型  ISA即指指令集架构(Instruction Set Architecture)

算法运行时 Running time


recurrence 递推式
递归：汉诺塔 二分查找 选择排序
选择排序T(n) = T(n-1) + 1     T(n)时间复杂度  T(n) = n
汉诺塔 T(n) = 2T(n-1)+1     
二分查找 T(n) = T(n/2) + 1 


4.11 第二次课



4.18 堆排序 贪心算法greedy

堆排序在比较排序中是最优算法。在最差和平均时间复杂度都是nlogn

排序问题可以规约为凸包问题
排序问题可以规约为欧式最小生成树问题

第三章 贪心算法 greedy
广义算法分类：求精算法、精准算法
本章为侠义求精算法即贪心算法 局部最优解的总和是全局最优解情况
典型贪心算法例子：从n个中拿出k个数使其和最大 每次都拿最大
特殊图上的最短路径

最小支撑树 minimum spanning tree（MST）最小生成树 Prim

kruskal's algorithm for finding MST

2-way merging problem。 n个list to one list


4.26 分而治之算法 split,recursive,merge
本课程都使用递归方式。
控制结构：顺序、分支、循环。递归
merge是分治算法的关键。
典型分治：二分查找、快速排序、归并排序

2D finding：
直接比较算法：比较所有点，复杂度O（n^2）
优化算法（分治）：1）split 分成左右2个区 2）recursive 递归左右两个区 3）merge策略 A区rank不变，A、B区根据Y值排序，B区根据Y的值修正。复杂度计算：O(rank(A)) + O(rank(B)) + O(recursive) + O(sorting) + O(merge) = O(1) + O(n) + O(2T(n/2)) + O(nlogn) + O(nlogn) = O(nlog^2n)

2D maxima finding:
直接比较算法：复杂度O(n^2)
分治算法：类似2D finding分治。merge策略：B区不变，A区根据B区的最大Y值修正。复杂度计算：O(rank(A)) + O(rank(B)) + O(recursive) + O(maxY(SR)) + O(merge) = O(n) + 2T(n/2) + O(n) = O(nlogn)

the closest pair problem: 找距离最近的两个点
分治算法：merge策略：? 复杂度O(nlogn)

the convex hull problem: 凸包问题
merge策略：merge 3 seq inito 1 seq, Graham Scan. 复杂度：O(n) + 2T(n/2) + Graham Scan O(n) = O(nlogn)

the voronoi diagram problem: 分地盘   —>  Delaunay triangulation
沃罗诺伊图（Voronoi Diagram，也称作Dirichlet tessellation，狄利克雷镶嵌）是由俄国数学家Georgy Fedoseevich Voronoi建立的空间分割算法。灵感来源于笛卡尔用凸域分割空间的思想。在几何，晶体学建筑学，地理学，气象学，信息系统等许多领域有广泛的应用。
泰森多边形又叫冯洛诺伊图（Voronoi diagram），得名于Georgy Voronoi，是由一组由连接两邻点直线的垂直平分线组成的连续多边形组成。北京奥运会的水立方即是基于此原理设计 。

复杂度 求递推式

可使用分治法求解的一些经典问题
（1）二分搜索
（2）大整数乘法
（3）Strassen矩阵乘法
（4）棋盘覆盖
（5）合并排序
（6）快速排序
（7）线性时间选择
（8）最接近点对问题
（9）循环赛日程表
（10）汉诺塔

5.9 树搜索
http://blog.csdn.net/hguisu/article/details/7709276
八皇后 定和0/1背包问题

5.16 裁剪与搜索 pruned search   O(n)    
典型特例：二分查找

selection problem, how to select p ? divide S into [n/5] subsets. subset(5) is 常量。  O(n)
T(n) = T((1-f)n) 递归复杂度 + O(n^k) 每个递归的复杂度 T(n) = O(n^k) 每次迭代的复杂度就是总的复杂度

2变量的线性规划问题 尽可能删除约束直线，找中值，删除对最小值没有影响的约束

The 1-center problem


5.23 动态规划 dynamic programming
多阶段图 multistage graph，树也是特殊例子
把问题描述成递推关系 找递推式
好处：消除一部分求解，系统性的方法
DP对子空间有重叠特别有效
fibonacci数列 递归为指数复杂度full call tree 复杂度2^n
指数-》多项式-》线性
DP优化：递归转递推 复杂度O（n）

归并算法和快速排序用动态规划？
最短路径
backward reasoning
forward reasoning

资源分配问题
TSP problem

递推式 -》复杂度

最长共有子串LCS problem-----重点理解 DNA



分而治之算法实例：
汉诺塔

2048游戏本质上可以抽象成信息对称双人对弈模型（玩家向四个方向中的一个移动，然后计算机在某个空格中填入2或4）。这里“信息对称”是指在任一时刻对弈双方对格局的信息完全一致，移动策略仅依赖对接下来格局的推理。作者使用的核心算法为对弈模型中常用的带Alpha-beta剪枝的Minimax。这个算法也常被用于如国际象棋等信息对称对弈AI中。

裁剪搜索算法(prune and search)举例：
对弈模型中常用的带Alpha-beta剪枝的Minimax算法，常被用于如国际象棋等信息对称对弈AI中。
