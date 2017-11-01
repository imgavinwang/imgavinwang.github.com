---
layout: post
title: 服务器端网络编程
date: 2014-09-07 20:20:20
categories:
- network
tags:
---

![servernetwork](/images/posts/2014-09-07-servernetwork-1.jpg)

**0.old one**

同步阻塞
fork并行模式
prefork模式 进程池

**1.master + worker**

程序结构：1）master accpt传递fd 给worker。2）

**2.单线程Reactor模式：非阻塞I/O + I/O多路复用**

程序结构：事件循环(event loop) + 事件处理
优点：
缺点：请求优先级不同问题。要求事件处理是非阻塞。对于请求响应式协议，容易割裂业务逻辑散落在不同的事件处理里，coroutine可以优化。
对于GG：有状态的会话，增加逻辑复杂度。非阻塞业务逻辑，数据库锁采用update for nowait模式。心跳。
常见应用：lighttpd/libevent/ACE

epoll技术的编程模型就是同步非阻塞回调，也可以叫做Reactor，事件驱动，事件轮循（EventLoop）。Epoll就是为了解决C10K问题而生。

**3.单线程Proactor模式**

常见应用：Boost.Asio

**4.actor模式**

模型结构：Actor模型=数据+行为+消息
Actor模型内部的状态由自己的行为维护，外部线程不能直接调用对象的行为，必须通过消息才能激发行为，这样就保证Actor内部数据只有被自己修改。
常见应用：Erlang原生提供/go   Akka框架/Disruptor框架 skynet

**5.one loop per thread + thread pool witch Reactor**

常见应用：moduo

**6.LMAX Disruptor**

http://martinfowler.com/articles/lmax.html
使用Disruptor这样无锁队列也可以自己实现Actor模型，让一个普通对象与外界的交互调用通过Disruptor消息队列实现，比如LMAX架构就是这样实现高频交易，从2009年成功运行至今，被Martin Fowler推崇。
Ring buffer更好、更快地在线程间共享数据的方法。
LMAX is a new retail financial trading platform. As a result it has to process many trades with low latency. The system is built on the JVM platform and centers on a Business Logic Processor that can handle 6 million orders per second on a single thread.

Log4j 2的最被认可的特点之一是“异步记录器”的性能。Log4j 2利用了LMAX Disruptor。例如，在相同的环境下，Log4j 2可以写每秒超过18,000,000条信息，而其他框架（像Logback和Log4j 1）每秒只能写< 2,000,000条消息。接近10倍？

**关于buffer**

- 每个网络连接开一个单独的固定长度的 buffer
- 用 memory pool 等改善内存使用率以及动态内存分配释放
- 使用 ring buffer 的优势是内存使用率很高，不会造成内存碎片，几乎没有浪费（比如传统动态内存分配需要的 cookie）。业务处理的同一时间，访问的内存数据段集中。可以更好的适应不同系统，取得较高的性能。内存的物理布局简单单一，不太容易发生内存越界、悬空指针等 bug ，出了问题也容易在内存级别分析调试。做出来的系统容易保持健壮。

**C10K C100K ...**
http://rango.swoole.com/archives/381
http://blog.acoak.com/article/c10k-problem-introduction
http://www.kegel.com/c10k.html
当程序员还沉浸在解决C10K问题带来的成就感时，一个新的问题被抛出了。异步嵌套回调太TM难写了。
协程的本质。协程是异步非阻塞的另外一种展现形式。Golang，Erlang，Lua协程都是这个模型。
视频直播 C100K问题 NGINX

多线程、事件驱动、ACTOR/CSP
http://weibo.com/ttarticle/p/show?id=2309403948698710187414#_rnd1457423402081
并发之痛 Thread，Goroutine，Actor

**分布式系统**

一个分布式系统由运行在多台机器上的多个服务进程组成，进程间采用TCP长连接通信，可随时scale out横向扩展。每类服务可以借助多线程来提高性能。

TCP长连接好处，容易定位服务之间的依赖关系。netstat -tn|grep :port


**关于金融交易系统**

对OLTP业务来说，单笔交易金额1分和100亿本质上的处理逻辑都一样（排除风控等因素），每秒处理事务数（TPS）及每分钟处理交易笔数（TPMC）才是衡量一个交易系统处理性能的标准。
一个交易系统最大的性能瓶颈在于关系数据库的锁机制的并发处理，传统方式是通过柔性事务、内存数据库、缓存、读写分离、Sharding等方式来降低数据库负荷及并发处理能力。
另外以前一个类似回答，供参考
现在有这样一个需求，在一秒中有3万的支付订单请求，有什么比较好的解决方案吗？ - 梁川的回答
柔性事务概念来自于BASE理论（BASE理论中的Soft State），可以参考 支付宝运营架构中柔性事务指的是什么？ - 梁川的回答

但以上方式还是存在性能瓶颈，像ACID锁、数据持久化等都是影响性能的因素。为了提高交易系统处理性能，英国的LMAX交易所采用了另外要一种思路：业务逻辑处理器完全是运行在内存中(in-memory)，使用事件源驱动方式(event sourcing)。业务逻辑处理器的核心是Disruptors，这是一个并发组件，能够在无锁的情况下实现网络的Queue并发操作。性能号称每秒能够处理600万笔订单。
关于LMAX的架构可以参考Martin Flower的文章 The LMAX Architecture
LMAX的开源框架 Disruptor by LMAX-Exchange
