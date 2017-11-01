---
layout: post
title: mysql安装
date: 2015-04-28 18:28:58
categories:
- mysql
tags:
---

```
安装mysql 服务器端：
          yum install mysql-server
 
          yum install mysql-devel
2. 安装mysql客户端：
          yum install mysql
3. 启动mysql服务：
          service mysqld start或者/etc/init.d/mysqld start
停止：
          service mysqld stop
重启：
          service mysqld restart
4. 创建root管理员：
          mysqladmin -u root password 123456
5.登陆
mysql -uroot -p123456


MySQL添加用户、删除用户与授权
MySql中添加用户,新建数据库,用户授权,删除用户,修改密码(注意每行后边都跟个;表示一个命令语句结束):

1.新建用户
　　1.1 登录MYSQL：
　　@>mysql -u root -p
　　@>密码
　　1.2 创建用户：
　　mysql> insert into mysql.user(Host,User,Password) values("localhost","test",password("1234"));

　　这样就创建了一个名为：test 密码为：1234 的用户。

　　注意：此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录。

　　1.3 然后登录一下：
　　mysql>exit;
　　@>mysql -u test -p
　　@>输入密码
　　mysql>登录成功


2.为用户授权

　　授权格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";　
　　2.1 登录MYSQL（有ROOT权限），这里以ROOT身份登录：
　　@>mysql -u root -p
　　@>密码
　　2.2 首先为用户创建一个数据库(testDB)：
　　mysql>create database testDB;
　　2.3 授权test用户拥有testDB数据库的所有权限（某个数据库的所有权限）：
　　 mysql>grant all privileges on testDB.* to test@localhost identified by '1234';
 　　mysql>flush privileges;//刷新系统权限表
　　格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";　
　　2.4 如果想指定部分权限给一用户，可以这样来写:
　　mysql>grant select,update on testDB.* to test@localhost identified by '1234';
　　mysql>flush privileges; //刷新系统权限表
　　2.5 授权test用户拥有所有数据库的某些权限： 　 
　　mysql>grant select,delete,update,create,drop on *.* to test@"%" identified by "1234";
     //test用户对所有数据库都有select,delete,update,create,drop 权限。
　 //@"%" 表示对所有非本地主机授权，不包括localhost。（localhost地址设为127.0.0.1，如果设为真实的本地地址，不知道是否可以，没有验证。）
　//对localhost授权：加上一句grant all privileges on testDB.* to test@localhost identified by '1234';即可。

3. 删除用户
 　　@>mysql -u root -p
　　@>密码
 　　mysql>Delete FROM user Where User='test' and Host='localhost';
 　　mysql>flush privileges;
 　　mysql>drop database testDB; //删除用户的数据库
删除账户及权限：>drop user 用户名@'%';
　　　　　　　　>drop user 用户名@ localhost; 


4. 修改指定用户密码
  　　@>mysql -u root -p
  　　@>密码
  　　mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";
  　　mysql>flush privileges;
 
5. 列出所有数据库
　　mysql>show database;

6. 切换数据库
　　mysql>use '数据库名';

7. 列出所有表
　　mysql>show tables;

8. 显示数据表结构
　　mysql>describe 表名;

9. 删除数据库和数据表
　　mysql>drop database 数据库名;
　　mysql>drop table 数据表名;

```

```
字符集

1)配置文件修改
/etc/my.cnf
[client]
default-character-set = utf8

[mysql]
default-character-set = utf8

SHOW VARIABLES LIKE 'character%';

2)进入数据库修改
mysql> SET character_set_client = utf8 ;  
mysql> SET character_set_connection = utf8 ;   
mysql> SET character_set_database = utf8 ;   
mysql> SET character_set_results = utf8 ;    
mysql> SET character_set_server = utf8 ;   
 
mysql> SET collation_connection = utf8 ;  
mysql> SET collation_database = utf8 ;   
mysql> SET collation_server = utf8 ; 

3)linux字符集
/etc/profile

export LANG="zh_CN.UTF-8"
```


{% highlight c++ %}
#include <mysql/mysql.h>
#include <stdio.h>
#include <stdlib.h>
//g++ -Wall mysql_test.c -o mysql_test -lmysqlclient
int main() 
{
    MYSQL *conn;
    MYSQL_RES *res;
    MYSQL_ROW row;
    char server[] = "localhost";
    char user[] = "root";
    char password[] = "0709";
    char database[] = "mysql";
    
    conn = mysql_init(NULL);
    
    if (!mysql_real_connect(conn, server,user, password, database, 0, NULL, 0)) 
    {
        fprintf(stderr, "%s\n", mysql_error(conn));
        exit(1);
    }
    
    if (mysql_query(conn, "show tables")) 
    {
        fprintf(stderr, "%s\n", mysql_error(conn));
        exit(1);
    }
    
    res = mysql_use_result(conn);
    
    printf("MySQL Tables in mysql database:\n");
    
    while ((row = mysql_fetch_row(res)) != NULL)
    {
        printf("%s \n", row[0]);
    }
    
    mysql_free_result(res);
    mysql_close(conn);
    
    printf("finish! \n");
    return 0;
}
{% endhighlight %}
