---
layout: post
title: libco测试实例分析
date: 2017-03-29 18:28:58
categories:
- c/c++
- opensource
tags:
---

**服务端测试例子 example_echosvr**

```
./example_echosvr 127.0.0.1 8889 1 1
```

``` c++
#include "co_routine.h"

#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <sys/time.h>
#include <stack>

#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/un.h>
#include <fcntl.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <sys/wait.h>  

using namespace std;
struct task_t
{
	//协程 保存协程运行时所有信息
	stCoRoutine_t *co;
	int fd;
};

//协程任务栈
static stack<task_t*> g_readwrite;
static int g_listen_fd = -1;

//非阻塞
static int SetNonBlock(int iSock)
{
    int iFlags;

    iFlags = fcntl(iSock, F_GETFL, 0);
    iFlags |= O_NONBLOCK;
    iFlags |= O_NDELAY;
    int ret = fcntl(iSock, F_SETFL, iFlags);
    return ret;
}

//协程任务入口函数
static void *readwrite_routine( void *arg )
{
	pid_t pid = getpid();
	printf("[%d] readwrite_routine start\n", pid);
	co_enable_hook_sys();

	task_t *co = (task_t*)arg;
	char buf[ 1024 * 16 ];
	for(;;)
	{
		//表示当前没有就绪的任务要处理
		if( -1 == co->fd )
		{
			//当前协程入队列
			g_readwrite.push( co );
			printf("[%d] g_readwrite.push(co)\n", pid);
			// 挂起当前协程，让出执行权给其他协程。
			// 原则很简单，就是让上次挂起的协程执行，可以认为是返回到上次执行的运行点。
			printf("[%d] co_yield_ct() readwrite_routine\n", pid);
			co_yield_ct();
			printf("[%d] continue readwrite_routine\n", pid);
			continue;
		}

		int fd = co->fd;
		co->fd = -1;

		for(;;)
		{
			struct pollfd pf = { 0 };
			pf.fd = fd;
			pf.events = (POLLIN|POLLERR|POLLHUP);
			printf("[%d] co_poll co_get_epoll_ct readwrite_routine fd[%d] \n", pid, fd);
			co_poll( co_get_epoll_ct(),&pf,1,1000);

			int ret = read( fd,buf,sizeof(buf) );
			printf("[%d] recv:%s\n", pid, buf);
			if( ret > 0 )
			{				
				printf("[%d] send:%s\n", pid, buf);
				ret = write( fd,buf,ret );
			}
			if( ret <= 0 )
			{
				close( fd );
				break;
			}
		}

	}
	return 0;
}
int co_accept(int fd, struct sockaddr *addr, socklen_t *len );
static void *accept_routine( void * )
{
	pid_t pid = getpid();
	co_enable_hook_sys();
	printf("[%d] accept_routine start\n", pid);
	fflush(stdout);
	for(;;)
	{
		//printf("pid %ld g_readwrite.size %ld\n",getpid(),g_readwrite.size());
		// 如果工作协程队列为空，就等待1秒或者等再来事件，重试
		if( g_readwrite.empty() )
		{
			printf("[%d] accept_routine g_readwrite.empty\n", pid); //sleep
			struct pollfd pf = { 0 };
			pf.fd = -1;
			poll( &pf,1,1000);

			continue;

		}
		struct sockaddr_in addr; //maybe sockaddr_un;
		memset( &addr,0,sizeof(addr) );
		socklen_t len = sizeof(addr);

		//printf("co_accept accept_routine\n");
		int fd = co_accept(g_listen_fd, (struct sockaddr *)&addr, &len);
		// 未就绪，等待下次事件继续处理
		if( fd < 0 )
		{
			struct pollfd pf = { 0 };
			pf.fd = g_listen_fd;
			pf.events = (POLLIN|POLLERR|POLLHUP);
			//　当前运行在accept协程，co_poll会在等待事件的时候交出cpu，回到主进程
			//printf("co_poll co_get_epoll_ct accept_routine\n");
			co_poll( co_get_epoll_ct(),&pf,1,1000 );
			continue;
		}
		// Fun！这里工作协程用尽，直接关闭当前连接...
		if( g_readwrite.empty() )
		{
			close( fd );
			continue;
		}
		// 弹出一个协程，去处理新连接
		SetNonBlock( fd );
		task_t *co = g_readwrite.top();
		co->fd = fd;
		printf("[%d] g_readwrite.pop() fd = %d\n", pid, fd);
		g_readwrite.pop();
		// 此时执行权会转移到某个线程，直到它交出cpu，当前协程才会再次执行
		printf("[%d] co_resume g_readwrite.top\n", pid);
		co_resume( co->co );
	}
	return 0;
}

static void SetAddr(const char *pszIP,const unsigned short shPort,struct sockaddr_in &addr)
{
	bzero(&addr,sizeof(addr));
	addr.sin_family = AF_INET;
	addr.sin_port = htons(shPort);
	int nIP = 0;
	if( !pszIP || '\0' == *pszIP   
	    || 0 == strcmp(pszIP,"0") || 0 == strcmp(pszIP,"0.0.0.0") 
		|| 0 == strcmp(pszIP,"*") 
	  )
	{
		nIP = htonl(INADDR_ANY);
	}
	else
	{
		nIP = inet_addr(pszIP);
	}
	addr.sin_addr.s_addr = nIP;

}

static int CreateTcpSocket(const unsigned short shPort /* = 0 */,const char *pszIP /* = "*" */,bool bReuse /* = false */)
{
	int fd = socket(AF_INET,SOCK_STREAM, IPPROTO_TCP);
	if( fd >= 0 )
	{
		if(shPort != 0)
		{
			if(bReuse)
			{
				int nReuseAddr = 1;
				setsockopt(fd,SOL_SOCKET,SO_REUSEADDR,&nReuseAddr,sizeof(nReuseAddr));
			}
			struct sockaddr_in addr ;
			SetAddr(pszIP,shPort,addr);
			int ret = bind(fd,(struct sockaddr*)&addr,sizeof(addr));
			if( ret != 0)
			{
				close(fd);
				return -1;
			}
		}
	}
	return fd;
}


int main(int argc,char *argv[])
{
	const char *ip = argv[1];
	int port = atoi( argv[2] );
	int cnt = atoi( argv[3] );
	int proccnt = atoi( argv[4] );

	int pid = 0;
    if( (pid = fork()) > 0 ) {
        exit(0);
    }
    setsid();
    if( (pid = fork()) > 0 ) {
        exit(0);
    }

	g_listen_fd = CreateTcpSocket( port,ip,true );
	listen( g_listen_fd,1024 );
	printf("listen %d %s:%d\n",g_listen_fd,ip,port);

	SetNonBlock( g_listen_fd );

	for(int k=0;k<proccnt;k++)
	{
		//proccnt进程数
		pid_t pid = fork();
		if( pid > 0 )
		{
			//父进程
			continue;
		}
		else if( pid < 0 )
		{
			break;
		}

		//子进程继续流程 协程数
		for(int i=0;i<cnt;i++)
		{
			task_t * task = (task_t*)calloc( 1,sizeof(task_t) );
			task->fd = -1;

			// co_create 协程任务运行入口函数 readwrite_routine 参数task
			printf("[%d] co_create readwrite_routine.\n", getpid());
			co_create( &(task->co),NULL,readwrite_routine,task );
			// co_resume 恢复协程执行 底层都会调用co_swap进行在独立栈/共享栈环境下的切换
			printf("[%d] co_resume readwrite_routine\n", getpid());
			co_resume( task->co );

		}
		stCoRoutine_t *accept_co = NULL;
		// 启动一个协程专门做accept
		printf("[%d] co_create accept_routine.\n", getpid());
		co_create( &accept_co,NULL,accept_routine,0 );
		// accept_co会一直接受新连接，直到它交出执行权，才会重新回到主进程
		printf("[%d] co_resume accept_routine.\n", getpid());
		co_resume( accept_co );

		printf("[%d] co_eventloop.pid\n", getpid());
		co_eventloop( co_get_epoll_ct(),0,0 );

		exit(0);
	}
	wait(NULL);

	return 0;
}



```

客户端连接测试例子

```
./client.pl 127.0.0.1 8889
```

``` perl
#!/usr/bin/perl -w 
use strict; 
use IO::Socket; 

main:
{
  return -1 if(@ARGV < 2);
    my $host = $ARGV[0]; 
    my $port = $ARGV[1];  
    my $sock = new IO::Socket::INET( PeerAddr => $host, PeerPort => $port, Proto => 'tcp'); 
    $sock or die "no socket :$!"; 
    my $msg;

    my $i = 1;
    while($i < 5)
    {
        $sock->send("hello srv");
        print "send:hello srv\n";
        $sock->recv($msg, 1024);
        print "recv:" . $msg . "\n";
        $i++;
    }
    close $sock;
}
```

日志

```shell
(myenv)root@gavin-VirtualBox:/media/sf_share/libco# ./example_echosvr 127.0.0.1 8889 1 2
listen 3 127.0.0.1:8889
[7630] co_create readwrite_routine.             -- 创建业务协程
[7630] co_resume readwrite_routine              -- 恢复业务协程
[7630] readwrite_routine start                  -- 业务协程执行
[7630] g_readwrite.push(co)                     -- 业务协程空闲入队列
[7630] co_yield_ct() readwrite_routine          -- 业务协程挂起
[7630] co_create accept_routine.                -- 创建接入协程
[7630] co_resume accept_routine.                -- 恢复接入协程
[7630] accept_routine start                     -- 接入协程执行
[7630] co_eventloop.pid                         -- 事件驱动

[7629] co_create readwrite_routine.
[7629] co_resume readwrite_routine
[7629] readwrite_routine start
[7629] g_readwrite.push(co)
[7629] co_yield_ct() readwrite_routine
[7629] co_create accept_routine.
[7629] co_resume accept_routine.
[7629] accept_routine start
[7629] co_eventloop.pid

(myenv)root@gavin-VirtualBox:/media/sf_share/libco# p example
root      7628     1  0 15:26 ?        00:00:00 ./example_echosvr 127.0.0.1 8889 1 2
root      7629  7628  1 15:26 ?        00:00:00 ./example_echosvr 127.0.0.1 8889 1 2
root      7630  7628  1 15:26 ?        00:00:00 ./example_echosvr 127.0.0.1 8889 1 2

[7630] g_readwrite.pop() fd = 5                         -- 弹栈业务协程处理新连接
[7630] co_resume g_readwrite.top                        -- 恢复业务协程
[7630] continue readwrite_routine
[7630] co_poll co_get_epoll_ct readwrite_routine fd[5]  -- 业务协程处理业务请求
[7630] accept_routine g_readwrite.empty                 -- 暂无可以业务协程处理新连接
[7630] recv:hello srv
[7630] send:hello srv
[7630] co_poll co_get_epoll_ct readwrite_routine fd[5]  -- 业务协程处理业务请求
[7630] recv:hello srv
[7630] send:hello srv
[7630] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7630] recv:hello srv
[7630] send:hello srv
[7630] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7630] recv:hello srv
[7630] send:hello srv
[7630] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7630] recv:hello srv
[7630] g_readwrite.push(co)                             -- 连接业务处理完毕入栈
[7630] co_yield_ct() readwrite_routine                  -- 挂起业务协程

[7629] g_readwrite.pop() fd = 5
[7629] co_resume g_readwrite.top
[7629] continue readwrite_routine
[7629] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7629] accept_routine g_readwrite.empty
[7629] recv:hello srv
[7629] send:hello srv
[7629] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7629] recv:hello srv
[7629] send:hello srv
[7629] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7629] recv:hello srv
[7629] send:hello srv
[7629] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7629] recv:hello srv
[7629] send:hello srv
[7629] co_poll co_get_epoll_ct readwrite_routine fd[5] 
[7629] recv:hello srv
[7629] g_readwrite.push(co)
[7629] co_yield_ct() readwrite_routine
```