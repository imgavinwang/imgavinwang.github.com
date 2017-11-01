---
layout: post
title: c语言多态函数指针
date: 2015-04-10 18:28:58
categories:
- c/c++
tags:
---

c++通过虚函数实现多态，c通过函数指针实现。

```
int int_add(int a, int b)
{
    return (a+b);
}
int int_sub(int a, int b)
{
    return (a-b);
}
int (*int_operator)(int, int) = int_add;
 
int _tmain(int argc, _TCHAR* argv[])
{
    cout<<int_operator(4, 5)<<endl; // output 9
    int_operator = int_sub;
    cout<<int_operator(4, 5)<<endl; // output -1
    return 0;
}
 ```

Libevent 把所有支持的 I/O demultiplex机制存储在一个全局静态数组 eventops 中，并在
初始化时选择使用何种机制，数组内容根据优先级顺序声明如下：  

```
/* In order of preference */ 
static const struct eventop *eventops[] = { 
#ifdef HAVE_EVENT_PORTS 
  &evportops, 
#endif 
#ifdef HAVE_WORKING_KQUEUE 
  &kqops, 
#endif 
#ifdef HAVE_EPOLL 
  &epollops, 
 #endif 
#ifdef HAVE_DEVPOLL 
  &devpollops, 
#endif 
#ifdef HAVE_POLL 
  &pollops, 
#endif 
#ifdef HAVE_SELECT 
  &selectops, 
#endif 
#ifdef WIN32 
  &win32ops, 
#endif 
  NULL 
};  
```

然后 libevent根据系统配置和编译选项决定使用哪一种 I/O demultiplex 机制，这段代码
在函数 event_base_new()中：   

```
  base->evbase = NULL; 
  for (i = 0; eventops[i] && !base->evbase; i++) { 
    base->evsel = eventops[i]; 
 
    base->evbase = base->evsel->init(base); 
 }  
 ```

  可以看出，libevent 在编译阶段选择系统的 I/O demultiplex 机制，而不支持在运行阶段
根据配置再次选择。   
以 Linux 下面的 epoll 为例，实现在源文件 epoll.c 中，eventops 对象 epollops定义如下：

```
const struct eventop epollops = { 
  "epoll", 
  epoll_init, 
  epoll_add, 
  epoll_del, 
  epoll_dispatch, 
  epoll_dealloc, 
 1 /* need reinit */ 
}; 
```

变量 epollops 中的函数指针具体声明如下，注意到其返回值和参数都和 eventop 中的定
义严格一致，这是函数指针的语法限制。   

```
static void *epoll_init  (struct event_base *); 
static int epoll_add (void *, struct event *); 
static int epoll_del (void *, struct event *); 
static  int epoll_dispatch(struct event_base *,  void *,  struct timeval *); 
static void epoll_dealloc  (struct event_base *, void *); 
```
