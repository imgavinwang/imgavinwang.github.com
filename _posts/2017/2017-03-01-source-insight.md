---
layout: post
title: Source Insight写码照样可以飞起！
date: 2017-03-01 18:28:58
categories:
- tools
- article
tags:
---

Source Insight应该用来阅读代码的人比较多，因为跳转快啊，不用自己很熟悉项目，去手动寻找定义处。所以Source Insight用于阅读代码相当出色。

使用过很多IDE，vim、ue、sublime、vs code、 eclipse、atom,总觉有不尽人意的地方。不过sublime还是挺爱的，就是函数列表和中文支持还差点意思。

最后又回到老牌Source Insight，发现开发代码也是相当不错，因为跳转定位文件快啊，不用打开一堆文件，跳转前进后退足已。加上函数列表和项目文件列表快捷键快速显隐切换，眼睛顿时豁然开朗。  
再加上代码快捷键列表，和代码智能补全提醒，写码速度应该可以飞起。  
用github托管代码加上支持mac，家里mac和公司windows可以无缝切换。

基本上我用Source Insight只用简单配置，重定义tab为4个空格，定义下项目文件列表快捷键即可，其他都用默认。  

```
代码高亮 + 快速跳转定位 + 函数及文件列表快捷键 + 代码智能补全 + 代码快捷键 = 写码飞起！
```

飞码快捷键：

```
后退                                 : Alt+,, Thumb 1 Click

向前                                 : Alt+., Thumb 2 Click

回到该行的开始                       : Home

到一行的尾部                         : End

转到行                               : F5, Ctrl+G

到文件头部                           : Ctrl+Home

到文件底部                           : Ctrl+End, Ctrl+(KeyPad) End

到窗口底部                           : (KeyPad) End (小键盘的END)

左边缩进                             : F9

右边缩进                             : F10

搜索                                 : Ctrl+F

向后搜索                             : F3

向前搜索                             : F4

搜索选择的(比如选择了一个单词,shift+F3将搜索上一个)    : Shift+F3

搜索选择的(比如选择了一个单词,shift+F4将搜索下一个)    : Shift+F4

替换                                 : Ctrl+H

复制一行                             : Ctrl+K

转到下一个修改                       : Alt+(KeyPad) +

回到前一个修改                       : Alt+(KeyPad) -

```

快捷键列表：

```
退出程序                             : Alt+F4

重画屏幕                             : Ctrl+Alt+Space

完成语法                             : Ctrl+E

恰好复制该位置右边的该行的字符       : Ctrl+Shift+K

复制到剪贴板                         : Ctrl+Del

剪切一行                             : Ctrl+U

剪切该位置右边的该行的字符           : Ctrl+;

剪切到剪贴板                         : Ctrl+Shift+X

剪切一个字                           : Ctrl+,

插入一行                             : Ctrl+I

插入新行                             : Ctrl+Enter

加入一行                             : Ctrl+J

从剪切板粘贴                         : Ctrl+Ins

粘贴一行                             : Ctrl+P

重复上一个动作                       : Ctrl+Y

重新编号                             : Ctrl+R

重复输入                             : Ctrl+

智能重命名                           : Ctrl+'

关闭文件                             : Ctrl+W

关闭所有文件                         : Ctrl+Shift+W

新建                                 : Ctrl+N

转到下一个文件                       : Ctrl+Shift+N

打开                                 : Ctrl+O

重新装载文件                         : Ctrl+Shift+O

另存为                               : Ctrl+Shift+S

显示文件状态                         : Shift+F10

激活语法窗口                         : Alt+L

回到选择的开始                       : Ctrl+Alt+[

到块的下面                           : Ctrl+Shift+]

到块的上面                           : Ctrl+Shift+[

书签                                 : Ctrl+M

到选择部分的尾部                     : Ctrl+Alt+]

到下一个函数                         : 小键盘 +

上一个函数                           :   小键盘 -

后退到索引                           : Alt+M

转到下一个链接                       : Shift+F9, Ctrl+Shift+L

跳到连接(就是语法串口列表的地方)     : Ctrl+L

跳到匹配                             : Alt+]

下一页                               : PgDn, (KeyPad) PgDn

上一页                               : PgUp, (KeyPad) PgUp

向上滚动半屏                         : Ctrl+PgDn, Ctrl+(KeyPad) PgDn, (KeyPad) *

向下滚动半屏                        : Ctrl+PgUp, Ctrl+(KeyPad) PgUp, (KeyPad) /

左滚                                 : Alt+Left

向上滚动一行                         : Alt+Down

向下滚动一行                         : Alt+Up

右滚                                 : Alt+Right

选择一块                             : Ctrl+-

选择当前位置的左边一个字符           : Shift+Left

选择当前位置右边一个字符             : Shift+Right

选择一行                             : Shift+F6

从当前行其开始向下选择               : Shift+Down

从当前行其开始向上选择               : Shift+Up

选择上页                             : Shift+PgDn, Shift+(KeyPad) PgDn

选择下页                             : Shift+PgUp, Shift+(KeyPad) PgUp

选择句子(直到遇到一个 . 为止)        : Shift+F7, Ctrl+.

从当前位置选择到文件结束             : Ctrl+Shift+End

从当前位置选择到行结束               : Shift+End

从当前位置选择到行的开始             : Shift+Home

从当前位置选择到文件顶部             : Ctrl+Shift+Home

选择一个单词                         : Shift+F5

选择左边单词                         : Ctrl+Shift+Left

选择右边单词                         : Ctrl+Shift+Right

到文件顶部                           : Ctrl+Home, Ctrl+(KeyPad) Home

到窗口顶部                           : (KeyPad) Home

到单词左边(也就是到一个单词的开始)   : Ctrl+Left

到单词右边(到该单词的结束)           : Ctrl+Right

排列语法窗口(有三种排列方式分别按1,2,3次)        : Alt+F7

移除文件                             : Alt+Shift+R

同步文件                             : Alt+Shift+S

增量搜索(当用Ctrl + F 搜索,然后按F12就会转到下一个匹配)      : F12

替换文件                             : Ctrl+Shift+H

在多个文件中搜索                     : Ctrl+Shift+F

浏览本地语法(弹出该文件语法列表窗口,如果你光标放到一个变量/函数等,那么列出本文件该变量/函数等的信息)    : F8

浏览工程语法                         : F7, Alt+G

跳到基本类型(即跳到原型)             : Alt+0

跳到定义出(也就是声明)               : Ctrl+=, Ctrl+L Click (select), Ctrl+Double L Click

检查引用                             : Ctrl+/

语法信息(弹出该语法的信息)           : Alt+/, Ctrl+R Click (select)

高亮当前单词                         : Shift+F8

语法窗口(隐藏/显示语法窗口)          : Alt+F8

关闭窗口                             : Alt+F6, Ctrl+F4

最后一个窗口                         : Ctrl+Tab, Ctrl+Shift+Tab 

```