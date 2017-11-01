---
layout: post
title: callback pattern
date: 2017-04-21 18:28:58
categories:
- designpattern
tags:
---

c回调函数与C++回调类

常用回调方法:  
- 第一种Callback的方法是面向过程的，使用简单而且灵活，正如C语言本身。
- 第二种Sink的方法是面向对象的，在C++里使用较多， 可以在一个Sink里封装一组回调接口，适用于一系列比较固定的回调事件。
- 第三种Delegate的方法也是面向对象的，和Sink封装一组接口不同，Delegate的封装是以函数为单位，粒度比Sink更小更灵活。 
- signal/slot

C++ 标准中没能提供面向对象的函数指针. 面向对象的函数指针也被称为闭包(closures) 或委托(delegates), 在类似的语言中已经体现出了它的价值.

http://blog.csdn.net/jamesmf/article/details/7710122

http://www.chinaitlab.com/c/special/sjms/Index.html

http://www.cppblog.com/weiym/archive/2012/08/28/188515.html

http://blog.csdn.net/xie1xiao1jun/article/details/8262902

```
#include <iostream>
#include <vector>
#include <stdio.h>

using namespace std;

typedef void (*DownloadCallback)(const char* pURL, bool bOK);  
void DownloadFile(const char* pURL, DownloadCallback callback)  
{  
	cout << "\ncall back.\n" << endl;
    cout << "downloading: " << pURL << "" << endl;  
    callback(pURL, true);  
}  
void OnDownloadFinished(const char* pURL, bool bOK)  
{  
    cout << "OnDownloadFinished, URL:" << pURL << "    status:" << bOK << endl;  
}

class SpiSink
{
public:  
    virtual void OnDownloadFinished(const char* pURL, bool bOK) = 0; 
};

class MySpi: public IDownloadSink  
{  
public:    
    virtual void OnDownloadFinished(const char* pURL, bool bOK)  
    {  
        cout << "OnDownloadFinished, URL:" << pURL << "    status:" << bOK << endl;  
    }  
};  

class Api  
{  
public:  
    Api()  
    {
    	cout << "\nsink mode like ctp.\n" << endl;
    }  
  
    void DownloadFile(const char* pURL)  
    {  
        cout << "downloading: " << pURL << "" << endl;  
        if(m_pSink != NULL)  
        {  
            m_pSink->OnDownloadFinished(pURL, true);  
        }  
    }  
    void Registerspi(SpiSink* pSink)
    {
    	m_pSink = pSink;
    }
  
private:  
    SpiSink* m_pSink;  
};

/*
class CDownloadDelegateBase  
{  
public:  
    virtual void Fire(const char* pURL, bool bOK) = 0;  
};  
  
template<typename O, typename T>  
class CDownloadDelegate: public CDownloadDelegateBase  
{  
    typedef void (T::*Fun)(const char*, bool);  
public:  
    CDownloadDelegate(O* pObj = NULL, Fun pFun = NULL)  
        :m_pFun(pFun), m_pObj(pObj)  
    {  
    }  
     
    virtual void Fire(const char* pURL, bool bOK)  
    {  
        if(m_pFun != NULL  
            && m_pObj != NULL)  
        {  
            (m_pObj->*m_pFun)(pURL, bOK);  
        }  
    }  
  
private:  
    Fun m_pFun;  
    O* m_pObj;  
};  
  
template<typename O, typename T>  
CDownloadDelegate<O,T>* MakeDelegate(O* pObject, void (T::*pFun)(const char* pURL, bool))  
{  
    return new CDownloadDelegate<O, T>(pObject, pFun);  
}  
  
class CDownloadEvent  
{  
public:  
    ~CDownloadEvent()  
    {  
        vector<CDownloadDelegateBase*>::iterator itr = m_arDelegates.begin();  
        while (itr != m_arDelegates.end())  
        {  
            delete *itr;  
            ++itr;  
        }  
        m_arDelegates.clear();  
    }  
  
    void operator += (CDownloadDelegateBase* p)  
    {  
        m_arDelegates.push_back(p);  
    }  
  
    void operator -= (CDownloadDelegateBase* p)  
    {  
        ITR itr = remove(m_arDelegates.begin(), m_arDelegates.end(), p);  
  
        ITR itrTemp = itr;  
        while (itrTemp != m_arDelegates.end())  
        {  
            delete *itr;  
            ++itr;  
        }  
        m_arDelegates.erase(itr, m_arDelegates.end());  
    }  
  
    void operator()(const char* pURL, bool bOK)  
    {  
        ITR itrTemp = m_arDelegates.begin();  
        while (itrTemp != m_arDelegates.end())  
        {  
            (*itrTemp)->Fire(pURL, bOK);  
            ++itrTemp;  
        }  
    }  
  
private:  
    vector<CDownloadDelegateBase*> m_arDelegates;  
    typedef vector<CDownloadDelegateBase*>::iterator ITR;  
};  
  
  
class CMyDownloaderEx  
{  
public:  
    void DownloadFile(const char* pURL)  
    {  
        cout << "downloading: " << pURL << "" << endl;  
        downloadEvent(pURL, true);  
    }  
  
    CDownloadEvent downloadEvent;  
};  
  
class CMyFileEx  
{  
public:  
    void download()  
    {  
        CMyDownloaderEx downloader;  
        downloader.downloadEvent += MakeDelegate(this, &CMyFileEx::OnDownloadFinished);  
        downloader.DownloadFile("www.baidu.com");  
    }  
  
    virtual void OnDownloadFinished(const char* pURL, bool bOK)  
    {  
        cout << "OnDownloadFinished, URL:" << pURL << "    status:" << bOK << endl;  
    }  
};  
*/

int main(int argc, char* argv[])  
{  
  
  	//callback
    DownloadFile("www.baidu.com", OnDownloadFinished);  
  
  	//sink
    CMyFile f1;  
    f1.download();  
  	
  	//类似ctp的回调方式 sink
  	Api* api = new Api();
  	MySpi* spi = new MySpi();
  	api->Registerspi((SpiSink*)spi);
  	api->DownloadFile("www.baidu.com");

  	/*Delegate
  	CMyFileEx ff;  
    ff.download();
    */

    return 0;  
}  

```