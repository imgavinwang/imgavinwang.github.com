---
layout: post
title: libco协程库笔记
date: 2017-02-07 18:28:58
categories:
- c/c++
- opensource
tags:
---

**libco分析笔记：**  
一句话总结libco库的原理，在协程里面用同步风格编写代码，实际运作是事件驱动的有限状态机，由上层的进程/线程负责多核资源的使用。

- 协程模型，异步状态的保存与恢复是自动的，协程恢复执行的时候就是上一次退出时的上下文。
- 把协程的让出与恢复作为异步网络IO中的一次事件注册与回调
- Hook 同步与异步的切换
- 类pthread接口设计，通过co_create、co_resume等简单清晰接口即可完成协程的创建与恢复
- 共享栈 高效使用内存 千万级协程
- 协程切换使用了汇编
- 基于epoll/kqueue实现的小而轻的网络框架，及基于时间轮盘实现的高性能定时器
- libco支持的协程原语包括：co_create、co_resume、co_yield、co_yield_ct、co_release


**libco原语分析**

**libco支持的协程原语包括：co_create、co_resume、co_yield、co_yield_ct、co_release**

co_create()：创建一个协程。因为协程寄生于线程中的，所以每个线程需要有自己线程级别的资源来维护管理自己的协程。这里程序没有使用到线程局部存储TLS的方式，而是采用全局的stCoRoutineEnv_t类型的指针数组，然后采用线程tid进行索引的方式获取线程独立的存储结构。虽然定义上pid_t一般是int类型，但是系统一般不会用到这么大的范围，如果没有额外配置系统，默认最大的线程ID值定义在/proc/sys/kernel/pid_max为32768，所以这里使用上没什么问题，且空间浪费也不是很大。

```
structstCoRoutineEnv_t {
 stCoRoutine_t *pCallStack[ 128];
intiCallStackSize;
 stCoEpoll_t *pEpoll;
 stCoRoutine_t* pending_co; stCoRoutine_t* ocupy_co;
};
```

**异步事件：co_poll、co_eventloop、co_get_epoll_ct**

 co_get_epoll_ct() 就是用于查找返回当初在线程级初始化中，创建stCoRoutineEnv_t的时候所创建的stCoEpoll_t类型pEpoll指针。

co_eventloop() 就是对epoll_wait调用处理的一个死循环，epoll_wait会进行一个1ms超时的短时间blocking调用，然后将可用的Item添加到pstActiveList中去，然后检查超时的事件并把超时的事件也添加到pstActiveList中去。当收集了这么多的active事件后，接下来依次调用各个Item的pfnProcess函数(这个函数通常是执行co_resume唤醒协程)。

co_poll() 该接口不仅可以在用户协程程序中直接使用，在hook_sys中也是被hook成poll()的形式而被大量使用。其操作较为复杂，分为以下几个步骤：

(1). 首先创建stPoll_t对象，设置完描述符相关的参数后，最重要的是设置pfnProcess=OnPollProcessEvent、pArg=co_self()；当事件就绪后就是通过这个函数和参数将自己切换回来；

(2). 将自己添加到ctx->pTimeout队列中去。关于这个ctx->pTimeout队列，其实是一个简单的链表数组结构，可以维持60x1000ms=60s的超时间隔，然后新的Item要插入进去的话，是就算其相对表头绝对超时时间的偏移长度来定位到指定的数组位置并进行插入的。当然这个接口设计的有点问题，就是poll的系统调用可以设置timeout=-1表示永不超时，但是这里的超时是必须设置且不能超过相对超时表头(理论是)60s，使用起来可能会有些误解。通过这样的数据结构，每次epoll_wait循环后取超时事件就十分方便快速了。

(3). epoll_ctl通过EPOLL_CTL_XXX将实际的事件侦听添加到操作系统中去。这里才发现poll和epoll的事件类型好像不兼容，所以两者常常会用函数转来转去的。

(4). co_yield_env()将自己切换出去；

(5). 后续执行表明因为事件就绪或者超时的因素又被切换回来了，此时调用epoll_ctl将事件从操作系统中删除掉(这也暗示了是ONE SHOT模式的哦)，保存返回得到的就绪事件。

(6). 释放资源，本次调用完成。

**阻塞调用Hook包括：co_enable_hook_sys、co_disable_hook_sys、co_is_enable_sys_hook**

在libco里面，大多数的默认阻塞例程基本都打开了sys_hook的支持了。这个Hook的原理，就是通过glibc中dlfcn.h的dlsym和RTLD_NEXT结合起来，从而给标准库函数添加钩子或者产生一个wrapper的效果。比如下面的这个常见的默认阻塞的read()函数，在没有打开sys_hook或套接字是阻塞类型的情况下，通过RTLD_NEXT将直接调用后面链接库的原始标准read()版本，否则会插入一个poll的操作，当然这个poll本身也是打了Hook的，详细的内容后面会有介绍。
基本大多数的C库和系统调用函数都被打上了hook，当然原文档里面特别提到了gethostbyname()，有兴趣的可以单独深入了解一下。

**协程局部存储 co_setspecific、co_getspecific**

**协程同步 co_cond_alloc、co_cond_signal、co_cond_broadcast、co_cond_timedwait**


**参考资料**

[协程与libco http://vexhub.com/archives/9.html](http://vexhub.com/archives/9.html)  
[腾讯开源的 libco 号称千万级协程支持，那个共享栈模式原理是什么? https://www.zhihu.com/question/52193579](https://www.zhihu.com/question/52193579)  
[微信开源libco：简单易用高性能的协程库 https://www.qcloud.com/community/article/234](https://www.qcloud.com/community/article/234)  
[C/C++协程库libco：微信怎样漂亮地完成异步化改造 http://www.infoq.com/cn/articles/CplusStyleCorourtine-At-Wechat](http://www.infoq.com/cn/articles/CplusStyleCorourtine-At-Wechat)  
[腾讯libco协程库学习使用笔记 https://taozj.org/201611/learn-note-of-tencent-libco-coroutine.html](https://taozj.org/201611/learn-note-of-tencent-libco-coroutine.html)  
[http://www.360doc.com/content/16/0712/19/12140429_575026770.shtml](http://www.360doc.com/content/16/0712/19/12140429_575026770.shtml)  