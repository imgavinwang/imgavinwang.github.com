---
layout: post
title: Oracle SGA
date: 2017-03-18 18:28:58
categories:
- oracle
tags:
---

共享池（Shared Pool），共享池是SGA保留的区，用于存储如SQL、PL/SQL存储过程及包、数据字典、锁、字符集信息、安全属性等。

共享池是Oracle数据库的术语，是数据库实例（Instance）的内存中SGA的一部分，它具体包含如下部分：
库缓存（Library Cache）。该区存放有经过语法分析并且正确的SQL语句，并随时都准备被执行。具体包含：
共享SQL区（Shared Pool Area）
私有SQL区（Private SQL Area）
PL/SQL存储过程及包（PL/SQL Procedure and Package）
控制结构（Control Structure）
字典缓存（Dictionary Cache）

http://blog.csdn.net/haiross/article/details/10069071

```
select sql_text,VERSION_COUNT,INVALIDATIONS,PARSE_CALLS,OPTIMIZER_MODE,
PARSING_USER_ID,PARSING_SCHEMA_ID,ADDRESS,HASH_VALUE
from v$sqlarea where version_count >1000;

select count(*) from v$sqltext;

--命中率 
select namespace,pins,pinhits,reloads,invalidations from v$librarycache order by namespace;

通过前面几节的研究我们知道,如果这个sql有7023个子指针，那么意味着这些子指针都将存在于同一个Bucket的链表上。那么这也就意味着,如果同样SQL再次执行,Oracle将不得不搜索这个链表以寻找可以共享的SQL。这将导致大量的library cache latch的竞争。

这时候我开始猜测原因:

1.可能代码存在问题,在每次执行之前程序修改某些session参数,导致sql不能共性

2.可能是8.1.5的v$sqlarea记录存在问题,我们看到的结果是假象:)

3.Bug

V$SQLAREA 
　　本视图持续跟踪所有shared pool中的共享cursor，在shared pool中的每一条SQL语句都对应一列。本视图在分析SQL语句资源使用方面非常重要。 

V$SQLAREA中的信息列 

HASH_VALUE：SQL语句的Hash值。 
ADDRESS：SQL语句在SGA中的地址。 
这两列被用于鉴别SQL语句，有时，两条不同的语句可能hash值相同。这时候，必须连同ADDRESS一同使用来确认SQL语句。 
PARSING_USER_ID：为语句解析第一条CURSOR的用户 
VERSION_COUNT：语句cursor的数量 
KEPT_VERSIONS： 
SHARABLE_MEMORY：cursor使用的共享内存总数 
PERSISTENT_MEMORY：cursor使用的常驻内存总数 
RUNTIME_MEMORY：cursor使用的运行时内存总数。 
SQL_TEXT：SQL语句的文本（最大只能保存该语句的前1000个字符）。 
MODULE,ACTION：使用了DBMS_APPLICATION_INFO时session解析第一条cursor时的信息 

V$SQLAREA中的其它常用列 

SORTS: 语句的排序数 
CPU_TIME: 语句被解析和执行的CPU时间 
ELAPSED_TIME: 语句被解析和执行的共用时间 
PARSE_CALLS: 语句的解析调用(软、硬)次数 
EXECUTIONS: 语句的执行次数 
INVALIDATIONS: 语句的cursor失效次数 
LOADS: 语句载入(载出)数量 
ROWS_PROCESSED: 语句返回的列总数 

示例： 
1.查看消耗资源最多的SQL： 
Sql代码  收藏代码
SELECT hash_value, executions, buffer_gets, disk_reads, parse_calls  
FROM V$SQLAREA  
WHERE buffer_gets > 10000000 OR disk_reads > 1000000  
ORDER BY buffer_gets + 100 * disk_reads DESC;  

2.查看某条SQL语句的资源消耗： 
Sql代码  收藏代码
SELECT hash_value, buffer_gets, disk_reads, executions, parse_calls  
FROM V$SQLAREA  
WHERE hash_Value = 228801498 AND address = hextoraw('CBD8E4B0');  


查找前10条性能差的sql语句 
Sql代码  收藏代码
SELECT * FROM (select PARSING_USER_ID,EXECUTIONS,SORTS,COMMAND_TYPE,DISK_READS,sql_text FROM v$sqlarea  
order BY disk_reads DESC )where ROWNUM<10 ;  
```