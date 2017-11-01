---
layout: post
title: boost.python
date: 2013-04-19 18:28:58
categories:
- boost
- c/c++
tags:
---


[http://edyfox.codecarver.org/html/boost_python.html](http://edyfox.codecarver.org/html/boost_python.html)

[http://www.it165.net/pro/html/201507/46357.html](http://www.it165.net/pro/html/201507/46357.html)

[http://blog.csdn.net/majianfei1023/article/details/46781581](http://blog.csdn.net/majianfei1023/article/details/46781581)

python use c++ lib example:

my.cpp
```c
#include <boost/python/module.hpp>
#include <boost/python.hpp>	

void hello()
{
	std::cout << "hello cpp" << std::endl;
}

BOOST_PYTHON_MODULE(my)
{
	boost::python::def("hello", hello);
}

//g++ my.cpp -o my.so -shared -fPIC -I/usr/include/python2.6 -L/usr/lib/python2.6 -lboost_python
```

hello.py
```python
import my 

print my.hello()
```