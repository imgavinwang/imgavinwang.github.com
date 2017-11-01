---
layout: post
title: 程序占用CPU内存
date: 2014-04-21 18:28:58
categories:
- debug
tags:
---

##系统占用CPU、内存：##
###top###

```shell
$  top -b -n 1 |grep wangjun|grep op|awk '{print "cpu:"$9"%","mem:"$10"%"}'
cpu:2.0% mem:0.0%
```

###ps aux###

RSS-------------进程实际占用物理内存大小(kb)  
VSZ--------------任务虚拟地址空间的大小  

```shell
$ ps aux|head -1
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
$ ps aux|grep op|grep wangjun
wangjun  15260  0.0  0.0   4768   828 pts/5    S+   11:44   0:00 op
wangjun  15269  0.0  0.0 109404   868 pts/8    S+   11:44   0:00 grep --color=auto op

ngboss    9244140  0.5  1.0 4196784 4196796  pts/1 A    11时39分00秒  0:25 qs prov_sz
```

###status###

```shell
$ cat /proc/15330/status
Name:   op
State:  S (sleeping)
Tgid:   15330
Pid:    15330
PPid:   13775
TracerPid:      0
Uid:    1080    1080    1080    1080
Gid:    1000    1000    1000    1000
FDSize: 1024
Groups: 1000 
VmPeak:     4768 kB
VmSize:     4768 kB
VmLck:         0 kB
VmPin:         0 kB
VmHWM:       828 kB
VmRSS:       828 kB
VmData:      704 kB
VmStk:       136 kB
VmExe:         4 kB
VmLib:      1840 kB
VmPTE:        36 kB
VmSwap:        0 kB
Threads:        1
SigQ:   0/128249
SigPnd: 0000000000000000
ShdPnd: 0000000000000000
SigBlk: 0000000000000000
SigIgn: 0000000000000000
SigCgt: 0000000000000000
CapInh: 0000000000000000
CapPrm: 0000000000000000
CapEff: 0000000000000000
CapBnd: ffffffffffffffff
Cpus_allowed:   ffff
Cpus_allowed_list:      0-15
Mems_allowed:   00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000000,00000003
Mems_allowed_list:      0-1
voluntary_ctxt_switches:        2
nonvoluntary_ctxt_switches:     0
```

**输出解释**  
参数 解释
Name 应用程序或命令的名字  
State 任务的状态，运行/睡眠/僵死/  
SleepAVG 任务的平均等待时间(以nanosecond为单位)，交互式任务因为休眠次数多、时间长，它们的 sleep_avg  
也会相应地更大一些，所以计算出来的优先级也会相应高一些。  
Tgid 线程组号  
Pid 任务ID  
Ppid 父进程ID  
TracerPid 接收跟踪该进程信息的进程的ID号  
Uid Uid euid suid fsuid  
Gid Gid egid sgid fsgid  
FDSize 文件描述符的最大个数，file->fds  
Groups  
VmSize(KB) 任务虚拟地址空间的大小  
(total_vm-reserved_vm)，其中total_vm为进程的地址空间的大小，reserved_vm：进程在预留或特殊的内存间的物理页  
VmLck(KB) 任务已经锁住的物理内存的大小。锁住的物理内存不能交换到硬盘 (locked_vm)  
VmRSS(KB) 应用程序正在使用的物理内存的大小，就是用ps命令的参数rss的值 (rss)  
VmData(KB) 程序数据段的大小（所占虚拟内存的大小），存放初始化了的数据；  
(total_vm-shared_vm-stack_vm)  
VmStk(KB) 任务在用户态的栈的大小 (stack_vm)  
VmExe(KB) 程序所拥有的可执行虚拟内存的大小，代码段，不包括任务使用的库 (end_code-start_code)  
VmLib(KB) 被映像到任务的虚拟内存空间的库的大小 (exec_lib)  
VmPTE 该进程的所有页表的大小，单位：kb  
Threads 共享使用该信号描述符的任务的个数，在POSIX多线程序应用程序中，线程组中的所有线程使用同一个信号描述符。  
SigQ 待处理信号的个数  
SigPnd 屏蔽位，存储了该线程的待处理信号  
ShdPnd 屏蔽位，存储了该线程组的待处理信号  
SigBlk 存放被阻塞的信号  
SigIgn 存放被忽略的信号  
SigCgt 存放被俘获到的信号  
CapInh Inheritable，能被当前进程执行的程序的继承的能力  
CapPrm Permitted，进程能够使用的能力，可以包含CapEff中没有的能力，这些能力是被进程自己临时放弃的，CapEff是CapPrm的一个子集，进程放弃没有必要的能力有利于提高安全性  
CapEff Effective，进程的有效能力  