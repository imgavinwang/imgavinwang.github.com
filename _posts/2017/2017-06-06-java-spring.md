---
layout: post
title: Java . Spring
date: 2017-06-06 18:28:58
categories:
- java
- spring
tags:
---

java:  
- 反射（reflection Java 1.3之后），它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring就是通过反射来实现注入的。
- Servlet 
  Servlet 是一些遵从Java Servlet API的Java类，这些Java类可以响应请求。尽管Servlet可以响应任意类型的请求，但是它们使用最广泛的是响应web方面的请求。 Servlet必须部署在Java servlet容器才能使用。
- Bean
- 抽象类
- 接口

框架:
- SSH spring springMVC Hibernate
- SSM spring springMVC Mybatis

为了降低Java开发的复杂性，Spring采取了以下4种关键策略：
- 基于POJO的轻量级和最小侵入性编程；
- 通过依赖注入和面向接口实现松耦合； -- 装配wiring
- 基于切面和惯例进行声明式编程；
- 通过切面和模板减少样板式代码。

Spring:  Spring共有7个模块，分别为：核心容器、Spring 上下文、Spring AOP、Spring DAO、Spring ORM、Spring Web 模块、Spring MVC 框架，Spring是全面的和模块化的。Spring有分层的体系结构，这意味着你能选择使用它孤立的任何部分，它的架构仍然是内在稳定的。  
- IoC（Inversion of Control，控制倒转）。这是spring的核心，贯穿始终。所谓IoC，对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。    
  Spring所倡导的开发方式就是如此，所有的类都会在spring容器中登记，告诉spring你是个什么东西，你需要什么东西，然后spring会在系统运行到适当的时候，把你要的东西主动给你，同时也把你交给其他需要你的东西。所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。
  IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的。  
- AOP (Aspect Oriented Programming，面向切面编程)，是指通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术，是对面向对象思维方式的有力补充。 Spring AOP -> AspectJ
  切面Aspect,通知advice[before,after,after-returning,after-throwing,around], 切点cutpoint, 连接点joinpoint  
- Spring MVC 
  SpringMVC是一个基于DispatcherServlet的MVC框架，每一个请求最先访问的都是DispatcherServlet，DispatcherServlet负责转发每一个Request请求给相应的Handler，Handler处理以后再返回相应的视图(View)和模型(Model)，返回的视图和模型都可以不指定，即可以只返回Model或只返回View或都不返回。
- Spring Boost 
  spring-boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Boot致力于在蓬勃发展的快速应用开发领域（rapid application development）成为领导者。

创建应用对象之间协作关系的行为通常称为装配（wiring），这也是依赖注入（DI）的本质。配置DispatcherServlet:
- 在XML中进行显式配置。   xmlConfig
- 在Java中进行显式配置。  javaConfig
- 隐式的bean发现机制和自动装配。

JAVA对象区别：
- POJO 简单java对象Plain Old Java Object
- JavaBean 看作遵从特定命名约定的POJO。JavaBean符合一定规范编写的Java类，不是一种技术，而是一种规范。
- EJB(Enterprise JavaBean) 我认为它是一组"功能"JavaBean的集合。上面说了JavaBean是实现了一种规范的Java对象。

Spring数据访问哲学：服务对象->Repository接口->Repository实现 Repository模板<->Repository回调
ORM: 对象/关系映射（object-relational
mapping，ORM）  
- JDBC
- Hibernate
- Mybatis Mybatis 是一种“Sql Mapping”的ORM实现 不同的数据库需要写不同SQL
  iBATIS提供的持久层框架包括SQL Maps和Data Access Objects（DAO）MyBatis 消除了几乎所有的JDBC代码和参数的手工设置以及结果集的检索。MyBatis 使用简单的 XML或注解用于配置和原始映射，将接口和 Java 的POJOs（Plain Old Java Objects，普通的 Java对象）映射成数据库中的记录。
  1)DAO(mybatis)->Mapping(sql xml)->POJO(obj)->Service(logic)->ServiceImpl(transaction)
  2)分页 PageHelper 基于拦截器; easyui
  3)级联查询
  4)简化配置
- JDO Java Data Objects
- JPA Java Persistence API

Spring注入方式：
- set函数注入、构造函数注入   --耦合度较高
- IOC方式注入 interface
- 注解注入 Autowired、Resource、Qualifier、Service、Controller、Repository、Component

- 

Spring简化方法：
- 简化DAO Mapper Constant
- 自动生成 Generator

Spring Boost:
- starter 依赖组
- 自动配置
- CLI Groovy
- Actuaor

IDE:
- Eclipse
- IDEA

JAVA构建工具:
- Maven
  pom.xml web.xml
- Gradle  Groovy的紧凑脚本
  build.gradle web.xml
- Ant

安全框架:
- Spring Security
- Shiro

缓存：
- Ehcache
- Redis key-value pair

web服务器:
- Tomcat
- Jetty

RPC面向服务 SOAP REST面向资源

ERROR:
- Error 404 - Not Found.
  No context on this server matched or handled this request.
Contexts known to this server are:
- org.apache.ibatis.binding.BindingException: Invalid bound statement (not found): com.gavin.dao.PositionDAO.findAllPosition
  at org.apache.ibatis.binding.MapperMethod$SqlCommand.<init>(MapperMethod.java:196)

[spring ioc原理 http://blog.csdn.net/it_man/article/details/4402245](http://blog.csdn.net/it_man/article/details/4402245)  
[http://spring.io/docs/](http://spring.io/docs/)  
[spring实战 https://github.com/vonzhou/SpringInAction3](https://github.com/vonzhou/SpringInAction3)  
[Spring教程 http://www.yiibai.com/spring/](http://www.yiibai.com/spring/)  
[https://www.zhihu.com/question/22021742](https://www.zhihu.com/question/22021742)  
[通过eclipse新建spring项目 http://www.cnblogs.com/juncaoit/p/6894211.html](http://www.cnblogs.com/juncaoit/p/6894211.html)  
[sprint3.x 企业级实战 http://stamen.iteye.com/category/209694](http://stamen.iteye.com/category/209694)  
[注册码 http://idea.lanyus.com/](http://idea.lanyus.com/)  
[《spring实战》第四版的Spittr项目 http://blog.csdn.net/tjuyanming/article/details/58222209](http://blog.csdn.net/tjuyanming/article/details/58222209)  
[5分钟构建spring web mvc REST风格HelloWorld](http://jinnianshilongnian.iteye.com/blog/1996071)  
[Spring Boot——2分钟构建spring web mvc REST风格HelloWorld](http://jinnianshilongnian.iteye.com/blog/1997192)  
[使用Spring Boot来加速Java web项目的开发](http://www.cnblogs.com/rollenholt/p/3693055.html)  
[关于使用Gradle整合Springmvc构建JavaWeb项目的那点事](http://blog.csdn.net/u013285335/article/details/50529622)  
[Gradle一分钟实现Spring-MVC](http://www.cnblogs.com/SLKnate/p/spring-mvc-in-oneminitue-with-gradle.html)  
[IntelliJ的Gradle项目配置SpringMVC](http://www.jianshu.com/p/781982e708e0)   
[web.xml 详解](http://blog.csdn.net/u010796790/article/details/52098258)  
[使用Intellij Idea＋Gradle 搭建Java 本地开发环境](http://www.open-open.com/lib/view/open1479714322613.html)  
[Intellij IDEA创建基于Gradle的SpringMVC工程](http://m.blog.csdn.net/article/details?id=58605602)   
[SSM框架——详细整合教程（Spring+SpringMVC+MyBatis）](http://blog.csdn.net/gebitan505/article/details/44455235/)  
[ SSM框架——使用MyBatis Generator自动创建代码](http://blog.csdn.net/zhshulin/article/details/23912615)  
[手把手教你整合最优雅SSM框架：SpringMVC + Spring + MyBatis](http://blog.csdn.net/qq598535550/article/details/51703190)   
[IntelliJ IDEA 创建第一个Mybatis工程](http://blog.csdn.net/lucia_fanchen/article/details/49386327)  
[使用IDEA和gradle搭建Spring MVC和MyBatis开发环境](http://www.cnblogs.com/bojuetech/p/5844413.html)  
[在 Gradle 中使用 MyBatis Generator](http://www.jianshu.com/p/5c85becf5f73)  
[使用IDEA的gradle整合spring+springmvc+mybatis 采用javaconfig配置](http://www.cnblogs.com/huangyichun/p/6151274.html)  
[Shiro简介——《跟我学Shiro》](http://jinnianshilongnian.iteye.com/blog/2018936)  
[【持久化框架】SpringMVC+Spring4+Mybatis3集成，开发简单Web项目+源码下载](http://blog.csdn.net/jiuqiyuliang/article/details/45132493/)  
[Spring mvc+easyui做列表展示及分页](https://my.oschina.net/u/1998885/blog/492498?p=1)  
[Java工程师成神之路~](http://www.hollischuang.com/archives/489)  
[Spring Boot Reference Guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-installing-the-cli)  
[Spring教程](http://www.yiibai.com/spring/)