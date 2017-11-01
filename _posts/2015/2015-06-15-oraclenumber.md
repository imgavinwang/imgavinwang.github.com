---
layout: post
title: oracle number(m,n)
date: 2015-06-15 20:20:20
categories:
- oracle
tags:
---


```
Oracle中数据类型number(m,n)中m表示的是所有有效数字的位数，n表示的是小数位的位数。m的范围是1-38，即最大38位。

1> .NUMBER类型细讲：
Oracle   number   datatype   语法：NUMBER[(precision   [, scale])]
简称：precision   -->   p 
          scale   -->   s

NUMBER(p,   s)
范围：   1   <=   p   <= 38, 

       -84   <=   s   <= 127
        保存数据范围： -1.0e-130   <= number value  <   1.0e+126        
保存在机器内部的范围：   1   ~   22   bytes

有效位：从左边第一个不为0的数算起的位数。
s的情况：
s   >   0
      精确到小数点右边s位，并四舍五入。然后检验有效位是否   <=   p。
s   <   0
      精确到小数点左边s位，并四舍五入。然后检验有效位是否   <=   p   +   |s|。
s   =   0
      此时NUMBER表示整数。

 

eg:
Actual   Data       Specified   As     Stored   As
----------------------------------------
123.89                       NUMBER            123.89
123.89                       NUMBER(3)           124
123.89                       NUMBER(6,2)       123.89
123.89                       NUMBER(6,1)       123.9
123.89                       NUMBER(4,2)       exceeds   precision   (有效位为5,   5   >   4)
123.89                       NUMBER(6,-2)     100
.01234                       NUMBER(4,5)       .01234   (有效位为4)
.00012                       NUMBER(4,5)       .00012
.000127                      NUMBER(4,5)       .00013
.0000012                     NUMBER(2,7)       .0000012
.00000123                    NUMBER(2,7)       .0000012
1.2e-4                       NUMBER(2,5)       0.00012
1.2e-5                       NUMBER(2,5)       0.00001
123.2564                     NUMBER                 123.2564
1234.9876                    NUMBER(6,2)       1234.99
12345.12345                  NUMBER(6,2)       Error   (有效位为5+2   >   6)
1234.9876                    NUMBER(6)           1235   (s没有表示s=0)
12345.345                    NUMBER(5,-2)     12300
1234567                      NUMBER(5,-2)     1234600
12345678                     NUMBER(5,-2)     Error   (有效位为8   >   7)
123456789                    NUMBER(5,-4)     123460000
1234567890                   NUMBER(5,-4)     Error   (有效位为10   >   9)
12345.58                     NUMBER(*,   1)     12345.6
0.1                          NUMBER(4,5)       Error   (0.10000,   有效位为5   >   4)
0.01234567                   NUMBER(4,5)       0.01235
0.09999                      NUMBER(4,5)       0.09999
```