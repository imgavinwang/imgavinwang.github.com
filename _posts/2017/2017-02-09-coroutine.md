---
layout: post
title: coroutine协程历史
date: 2017-02-09 18:28:58
categories:
- coroutine
tags:
---

协程诞生解决的是低速IO和高速的CPU的协调问题，解决这类问题主要有三个有效途径：
- 异步非阻塞网络编程（libevent、libev、redis、Nginx、memcached这类）
- 协程（golang、gevent）
- “轻量级线程”，相当于是在语言层面做抽象（Erlang）

对比之下协程的编程难度较低，不要求编程人员要有那么高的抽象思维能力。再加上golang在这方面优秀的实践，协程目前的前途还是一片光明的。当然还有一点，我们要承认无论你状态机、callback设计得多么精妙，现实中阻塞事很难以避免的。避免了Network IO Blocking，还有Disk IO Blocking，还有数据库Blocking，还有日志Blocking，还有第三方库blocking，还有愚蠢的人类blocking……

协程是基于事件驱动的异步模型的封装，比线程和进程都要轻，而且它没有线程的资源共享的问题，进程通信的问题。

个人觉得solution可以这样（从C程序员的角度）：如果用协程就不要用线程(pthread)，一个进程随你开多少个协程，服务器案例（SRS）；如果用多线程，一个线程一个event loop(epoll)，服务器案例（Muduo） @陈硕 。两种都能发挥多核优势。


异步回调方案 典型如NodeJS，遇到阻塞的情况，比如网络调用，则注册一个回调方法（其实还包括了一些上下文数据对象）给IO调度器（linux下是libev，调度器在另外的线程里），当前线程就被释放了，去干别的事情了。等数据准备好，调度器会将结果传递给回调方法然后执行，执行其实不在原来发起请求的线程里了，但对用户来说无感知。但这种方式的问题就是很容易遇到callback hell，因为所有的阻塞操作都必须异步，否则系统就卡死了。还有就是异步的方式有点违反人类思维习惯，人类还是习惯同步的方式。
GreenThread/Coroutine/Fiber方案 这种方案其实和上面的方案本质上区别不大，关键在于回调上下文的保存以及执行机制。为了解决回调方法带来的难题，这种方案的思路是写代码的时候还是按顺序写，但遇到IO等阻塞调用时，将当前的代码片段暂停，保存上下文，让出当前线程。等IO事件回来，然后再找个线程让当前代码片段恢复上下文继续执行，写代码的时候感觉好像是同步的，仿佛在同一个线程完成的，但实际上系统可能切换了线程，但对程序无感。


[并发之痛 Thread，Goroutine，Actor](http://jolestar.com/parallel-programming-model-thread-goroutine-actor/)