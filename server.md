
## Server

![server](/images/posts/server-1.png)

**并发模式**

- old one 同步阻塞 fork并行模式 prefork模式 进程池
- 多线程 线程池 异步
- master + worker nginx
- 单线程Reactor模式：非阻塞I/O + I/O多路复用 select/poll/epoll/kqueue
- 单线程Proactor模式 常见应用：Boost.Asio
- Actor模式 常见应用：Erlang原生提供/go Akka框架/skynet c+lua
- one loop per thread + thread pool with Reactor 常见应用：moduo
- LMAX Disruptor 无锁RingBuffer
- libco 协程异步事件驱动 同步编程、异步调用
- IOCP 异步IO
- 协程 维护上下文 greenlet coroutine goroutine 协程阻塞线程阻塞
- router + queue + worker process

**I/O机制**

- BIO：Blocking IO  阻塞IO
  一个socket套接字需要一个进程或一个线程来处理
- NIO：Nonblocking IO  
  基于事件驱动（epool）思想，采用Reactor模式，每个socket套接字分配一个线程，一个线程可以处理多个套接字处理的工作
- AIO：异步 IO
  基于事件驱动思想，采用Proactor模式 Boost.Asio

**路由**

- 按属性归属 
- 负载均衡 轮询、均衡、取模、一致性哈希
- 轮询、权重轮询、响应速度均衡、最少连接均衡、处理能力均衡

**C10K C100K …**


**分布式系统**

- CAP：2000年7月 由 Eric Brewer提出, 并经过他人证明, 分布式系统不能同时满足CAP:Consistency一致性、Availability   可用性(指的是快速获取数据）、Tolerance of network Parttion    分区容错性： 即使网络出现故障从而分区, 不影响系统运行。AP：放弃C，大多数分布式系统都选择此项（并不是真正放弃，而是通过其它方式实现）。
- 两段式提交2PC
  一次事务首先要准备资源, 所有节点的资源都准备好后, 同时进行Commit（提交）, 如果中途中断则会一起ROLLBACK（回滚）, 从而实现数据一致性。
- 弱一致性（包括最终一致性）