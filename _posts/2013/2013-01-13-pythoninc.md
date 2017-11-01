---
layout: post
title: C++中嵌入Python调用[转]
date: 2013-01-13 18:28:58
categories:
- python
- c/c++
tags:
---


把python嵌入的C++里面需要做一些步骤

安装python程序，这样才能使用python的头文件和库
在我们写的源文件中增加“Python.h”头文件，并且链入“python**.lib”库（还没搞清楚这个库时静态库还是导出库，需要搞清楚）
掌握和了解一些python的C语言api，以便在我们的c++程序中使用
常用的一些C API函数

在了解下面的函数之前有必要了解一下**PyObject***指针，python里面几乎所有的对象都是使用这个指针来指示的。

Py_Initialize（）&&Py_Finalize（）

在调用任何python的c函数之前需要调用的函数，“Py_Initialize”是用来初始化python模块的，推测是加载初始化加载dll的。对应的在使用python模块之后用“Py_Finalize”来释放模块。
PyImport_ImportModule（）

用来载入一个python模块，这个模块就是一般的python文件。这里需要注意的是，在加载这个模块的时候会执行模块里面所有可以执行的语句。包括import导入语句和在函数体之外的所有语句
PyObject_GetAttrString（）

返回模块里面的函数
Py_BuildValue（）

建立一个参数元组，一般都是用这个函数来建立元组，然后将这个元组作为参数传递给python里面的函数。
PyEval_CallObject（）

调用函数，并把“Py_BuildValue”建立的元组作为参数传递给被调用的函数
源码实例

下面的实例是在c++代码中调用Python的函数，传递参数并且获取返回值

test.cpp代码

{% highlight c++ %}
#include <iostream>
#include <Python.h>
using namespace std;

int main(int argc, char* argv[])
{
    Py_Initialize();   //初始化

    PyObject* pModule = NULL;
    PyObject* pFunc = NULL;
    PyObject* pParam = NULL;
    PyObject* pResult = NULL;
    const char* pBuffer = NULL;
    int iBufferSize = 0;

    pModule = PyImport_ImportModule(“test_python");

    if (!pModule)
    {
        cout << "get module failed!" << endl;
        exit (0);
    }

    pFunc = PyObject_GetAttrString(pModule, "main");
    if (!pFunc)
    {
        cout << "get func failed!" << endl;
        cout << int(pFunc) << endl;
        exit (0);
    }
    pParam = Py_BuildValue("(s)", "HEHEHE");
    pResult = PyEval_CallObject(pFunc,pParam);
    if(pResult)
    {
        if(PyArg_Parse(pResult, "(si)", &pBuffer, iBufferSize))
        {
            cout << pBuffer << endl;
            cout << iBufferSize << endl;
        }
    }
    Py_DECREF(pParam);
    Py_DECREF(pFunc);

    Py_Finalize();
    //cout << "hello" << endl;
    return 0;
}
{% endhighlight %}


test_python.py代码

{% highlight c++ %}
def main(szString):
    return ("hello", 5)
{% endhighlight %}
