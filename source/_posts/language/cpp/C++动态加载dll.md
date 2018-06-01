---
title: C++动态加载Dll
categories: programming
tags:
  - c++
date: 2017-02-07 09:30:22
---

#### C++加载DLL

```c++
//C++
#include <windows.h>
typedef int(*Fun)(char*);
HINSTANCE mylib = LoadLibrary(L"licchecker.dll");
if (mylib==NULL)
{
	return -1;
}
Fun libFun = (Fun)GetProcAddress(mylib, "IsLicenseValid");
```
“IsLicenseValid”是DLL中定义的需要调用的函数名称,然后就可以直接调用了。

<!--more-->

#### Qt加载DLL
```c++
//QT
#include <QLibrary>
typedef int(*Fun)(char*);
int main(int argc, char *argv[])
{
QLibrary mylib("licchecker.dll");
if (mylib.load())
{
    Fun IsLicenseValid=(Fun)mylib.resolve("IsLicenseValid");
    if(IsLicenseValid(const_cast<char*>("abc"))){
        return 1;
    }
}
```
Qt中动态加载DLL的方式和原生C++基本类似,但是代码更加精简。


