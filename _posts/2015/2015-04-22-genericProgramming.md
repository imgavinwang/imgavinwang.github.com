---
layout: post
title: 泛型编程GenericProgramming
date: 2015-04-22 18:28:58
categories:
- language
tags:
---

泛型编程（Generic Programming）最初提出时的动机很简单直接：发明一种语言机制，能够帮助实现一个通用的标准容器库。  

泛型编程最初诞生于C++中，由Alexander Stepanov[2]和David Musser[3]创立。目的是为了实现C++的STL（标准模板库）。其语言支持机制就是模板（Templates）。模板的精神其实很简单：参数化类型。换句话说，把一个原本特定于某个类型的算法或类当中的类型信息抽掉，抽出来做成模板参数T。  

泛型编程的代表作品STL是一种高效、泛型、可交互操作的软件组件。  
STL以迭代器 (Iterators)和容器(Containers)为基础，是一种泛型算法(Generic Algorithms)库，容器的存在使这些算法有东西可以操作。STL包含各种泛型算法(algorithms)、泛型迭代器（iterators）、泛型容器(containers)以及函数对象(function objects)。  

STL并非只是一些有用组件的集合，它是描述软件组件抽象需求条件的一个正规而有条理的架构。  

