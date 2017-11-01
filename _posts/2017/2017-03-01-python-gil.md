---
layout: post
title: Python的GIL是什么鬼，多线程性能究竟如何[转]
date: 2017-03-01 18:28:58
categories:
- python
tags:
---

```
作者：卢钧轶(cenalulu) 本文原文地址：http://cenalulu.github.io/python/gil-in-python/
```

[http://cenalulu.github.io/python/gil-in-python/](http://cenalulu.github.io/python/gil-in-python/)

内容摘要：

**GIL是什么**

首先需要明确的一点是GIL并不是Python的特性，它是在实现Python解析器(CPython)时所引入的一个概念。就好比C++是一套语言（语法）标准，但是可以用不同的编译器来编译成可执行代码。有名的编译器例如GCC，INTEL C++，Visual C++等。Python也一样，同样一段代码可以通过CPython，PyPy，Psyco等不同的Python执行环境来执行。像其中的JPython就没有GIL。然而因为CPython是大部分环境下默认的Python执行环境。所以在很多人的概念里CPython就是Python，也就想当然的把GIL归结为Python语言的缺陷。所以这里要先明确一点：GIL并不是Python的特性，Python完全可以不依赖于GIL

那么CPython实现中的GIL又是什么呢？GIL全称Global Interpreter Lock为了避免误导，我们还是来看一下官方给出的解释：

```
In CPython, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from executing Python bytecodes at once. This lock is necessary mainly because CPython’s memory management is not thread-safe. (However, since the GIL exists, other features have grown to depend on the guarantees that it enforces.)
```

好吧，是不是看上去很糟糕？一个防止多线程并发执行机器码的一个Mutex，乍一看就是个BUG般存在的全局锁嘛！别急，我们下面慢慢的分析。


**GIL的影响**

从上文的介绍和官方的定义来看，GIL无疑就是一把全局排他锁。毫无疑问全局锁的存在会对多线程的效率有不小影响。甚至就几乎等于Python是个单线程的程序。 那么读者就会说了，全局锁只要释放的勤快效率也不会差啊。只要在进行耗时的IO操作的时候，能释放GIL，这样也还是可以提升运行效率的嘛。或者说再差也不会比单线程的效率差吧。理论上是这样，而实际上呢？Python比你想的更糟。


**当前GIL设计的缺陷**

基于pcode数量的调度方式

按照Python社区的想法，操作系统本身的线程调度已经非常成熟稳定了，没有必要自己搞一套。所以Python的线程就是C语言的一个pthread，并通过操作系统调度算法进行调度（例如linux是CFS）。为了让各个线程能够平均利用CPU时间，python会计算当前已执行的微代码数量，达到一定阈值后就强制释放GIL。而这时也会触发一次操作系统的线程调度（当然是否真正进行上下文切换由操作系统自主决定）。

**如何避免受到GIL的影响**

说了那么多，如果不说解决方案就仅仅是个科普帖，然并卵。GIL这么烂，有没有办法绕过呢？我们来看看有哪些现成的方案。

用multiprocessing替代Thread

multiprocessing库的出现很大程度上是为了弥补thread库因为GIL而低效的缺陷。它完整的复制了一套thread所提供的接口方便迁移。唯一的不同就是它使用了多进程而不是多线程。每个进程有自己的独立的GIL，因此也不会出现进程之间的GIL争抢。

当然multiprocessing也不是万能良药。它的引入会增加程序实现时线程间数据通讯和同步的困难。就拿计数器来举例子，如果我们要多个线程累加同一个变量，对于thread来说，申明一个global变量，用thread.Lock的context包裹住三行就搞定了。而multiprocessing由于进程之间无法看到对方的数据，只能通过在主线程申明一个Queue，put再get或者用share memory的方法。这个额外的实现成本使得本来就非常痛苦的多线程程序编码，变得更加痛苦了。具体难点在哪有兴趣的读者可以扩展阅读这篇文章

用其他解析器

之前也提到了既然GIL只是CPython的产物，那么其他解析器是不是更好呢？没错，像JPython和IronPython这样的解析器由于实现语言的特性，他们不需要GIL的帮助。然而由于用了Java/C#用于解析器实现，他们也失去了利用社区众多C语言模块有用特性的机会。所以这些解析器也因此一直都比较小众。毕竟功能和性能大家在初期都会选择前者，Done is better than perfect。

所以没救了么？

当然Python社区也在非常努力的不断改进GIL，甚至是尝试去除GIL。并在各个小版本中有了不少的进步。有兴趣的读者可以扩展阅读这个Slide 另一个改进Reworking the GIL - 将切换颗粒度从基于opcode计数改成基于时间片计数 - 避免最近一次释放GIL锁的线程再次被立即调度 - 新增线程优先级功能（高优先级线程可以迫使其他线程释放所持有的GIL锁）

**总结**

Python GIL其实是功能和性能之间权衡后的产物，它尤其存在的合理性，也有较难改变的客观因素。从本分的分析中，我们可以做以下一些简单的总结： - 因为GIL的存在，只有IO Bound场景下得多线程会得到较好的性能 - 如果对并行计算性能较高的程序可以考虑把核心部分也成C模块，或者索性用其他语言实现 - GIL在较长一段时间内将会继续存在，但是会不断对其进行改进