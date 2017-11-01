---
layout: post
title: thread safe线程安全
date: 2013-05-02 18:28:58
categories:
- thread
tags:
---

线程安全

对共享资源的安全使用 不会把数据写脏

- 可重入
- 原子性

根据《Java Concurrency in Practice》的定义，一个线程安全的 class 应当满足以下三个条件：
• 多个线程同时访问时，其表现出正确的行为。
• 无论操作系统如何调度这些线程， 无论这些线程的执行顺序如何交织（interleaving）。
• 调用端代码无须额外的同步或其他协调动作。
