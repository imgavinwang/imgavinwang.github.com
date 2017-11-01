---
layout: post
title: 智能指针 smart_ptr
date: 2016-03-07 18:28:58
categories:
- boost
- c/c++
tags:

---

smart_ptr: 智能指针的原理基于一个常见的习语叫做 RAII 

- std::auto_ptr  
  它基本上就像是个普通的指针： 通过地址来访问一个动态分配的对象。 std::auto_ptr 之所以被看作是智能指针，是因为它会在析构的时候调用 delete 操作符来自动释放所包含的对象。
- scoped_ptr 作用域指针 scoped_array
  一个作用域指针不能传递它所包含的对象的所有权到另一个作用域指针。 一旦用一个地址来初始化，这个动态分配的对象将在析构阶段释放。保证指针的绝对安全。
- shared_ptr 共享指针 shared_array
  智能指针 boost::shared_ptr 基本上类似于 boost::scoped_ptr。 关键不同之处在于 boost::shared_ptr 不一定要独占一个对象。 它可以和其他 boost::shared_ptr 类型的智能指针共享所有权。 在这种情况下，当引用对象的最后一个智能指针销毁后，对象才会被释放。

  因为所有权可以在 boost::shared_ptr 之间共享，任何一个共享指针都可以被复制，这跟 boost::scoped_ptr 是不同的。 这样就可以在标准容器里存储智能指针了——你不能在标准容器中存储 std::auto_ptr，因为它们在拷贝的时候传递了所有权。
- weak_ptr 弱指针
  boost::weak_ptr 必定总是通过 boost::shared_ptr 来初始化的。一旦初始化之后，它基本上只提供一个有用的方法: lock()。此方法返回的boost::shared_ptr 与用来初始化弱指针的共享指针共享所有权。 如果这个共享指针不含有任何对象，返回的共享指针也将是空的。
