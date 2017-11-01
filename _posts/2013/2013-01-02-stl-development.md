---
layout: post
title: STL的历史
date: 2013-01-02 20:20:20
categories:
- c/c++
- language
tags:
---

什么是STL
    STL就是C++ Standard Template Library，也就是标准模版库，是一个高效的C++程序库。STL包含六大组件：容器（container）、算法（algorithm）、迭代器（iterator）、配置器（allocator）、适配器（adapter）和函数对象（function object）。我们在学习这些组件时，应该按其重要程度来区别学习。重要成都由大到小是：泛型容器和泛型算法（表示任何类型和对象都可以使用这些容器和算法）>>迭代器>>配置器、适配器、函数对象。
    STL的主要头文件包括13个，分别是<algorithm>、<deque>、<functional>、<iterator>、<vector>、<list>、<map>、<numeric>、<memory>、<queue>、<set>、<stack>、<utility>，具体内容如表1所示。
    用概括性的语言来说，STL的各部分作用是：
        1）为了方便地存储数据，设计了容器；
        2）为了方便地遍历、查找、替换容器中的元素，设计了算法；
        3）为了方便地让容器、算法独立工作，设计了在二者之间起桥梁作用的迭代器；
        4）为了统一分配和控制容器中的内存，设计了配置器；
        5）为了更好地给算法传入参数，设计了函数对象；
        6）为了更好地扩展STL现有的接口，设计了适配器。

STL的历史
    在STL的发展史中，总共产生了5个版本。
    2.1 HP STL——第一个STL版本
    HP STL版本，就是惠普版本的STL库，是所有其他STL版本的鼻祖。其他版本的STL都是在此版本的基础上加以改进和实现的。
    2.2 P.J. Plauger STL——Microsoft Visual C++的STL版本
    P.J. Plauger STL版本是由P.J. Plauger个人实现的，是HP STL版本的继承版本。Microsoft Visual C++中的STL库用的就是P.J. Plauger STL版本。P.J. Plauger STL版本不是开放源代码的，它的元代那是不能修改或销售的。
    2.3 Rouge Wave STL——Borland C++ Builder5.0的STL版本
    Rouge Wave STL版本是由Rouge Wave公司开发的，也是HP STL版本的继承版本。该版本被Borland C++ Builder5.0所采用，可惜的是由于该版本长时间没有被更新过且它不完全符合STL标准，最后被弃用。
    2.4 STLport——Borland C++ Builder6.0的STL版本
    STLport版本的STL最开始是俄罗斯人的一个开发项目，主要用途是把UNIX下的SGI STL代码一直到其他平台的主流编译器上，例如C++ Borland或Visual C++等。STLport版本的STL符合ANSI/C++的STL标准，因此也更容易移植。Borland C++ Builder6.0中的STL用的就是STLport版本。
    2.5 SGI STL——GCC的STL版本
    SGI STL室友Silicon Graphics Computer System，Inc公司开发的，SGI STL同样也是HP STL版本的继承版本。Linux下的GCC编译器采用的正式SGI STL版本。GCC对C++语言标准的支持非常好，SGI STL在Linux平台上的性能非常出色。SGI STL是属于开发源代码的，因此读者可以修改和销售SGISTL版本。
