---
title: C++命令行-gflags
date: 2017-01-14 11:12:39
categories: others
tags:
  - gflags
---

### gflags简介

>gflags 是谷歌开源的用于命令行参数处理的库,有c++/python语言版本,支持从环境变量/配置文件中读取参数.

### CMakeLists.txt
```cmake
cmake_minimum_required(VERSION 3.0.0)
project(gflags_test VERSION 0.1.0)
find_library(GFLAGS_LIBS NAMES gflags)
find_path(GFLAGS_INCLUDE_DIRS NAMES gflags/gflags.h)
include_directories(GFLAGS_INCLUDE_DIRS)
add_executable(gflags_test main.cpp)
target_link_libraries(gflags_test ${GFLAGS_LIBS})
```


### main.cpp
```c++
#include <iostream>
#include <gflags/gflags.h>

static bool validateDouble(const char* name,double value)
{
    if(value>0 && value<255)
    {
        return true;
    }
    std::cout<<"value must in range 0~255"<<std::endl;
    return false;
}

DEFINE_double(double_val, 123, "double value");
DEFINE_string(string_val, "hello world", "string value");
DEFINE_bool(bool_val, true, "bool value");

//使用全局静态函数,保证在main函数执行之前进行注册
static const bool double_dummy=gflags::RegisterFlagValidator(&FLAGS_double_val,&validateDouble);

int main(int argc, char **argv)
{
    //设置版本信息: 
    //>>./main --version
    //>>main version beta 1.0.0  
    gflags::SetVersionString("beta 1.0.0");

    //设置使用(帮助)信息
    //>>./main --help  
    //>>main: copyright@:李鹏程
    gflags::SetUsageMessage("copyright@:李鹏程");

    //>>./main -double_val=456 
    //会提示错误然后直接退出,下面的程序不会执行
    gflags::ParseCommandLineFlags(&argc, &argv, true);

    //>>./main
    //会提示错误,但依然会运行,值会被改为默认值
    //如果默认值也错误,则直接退出
    // gflags::SetCommandLineOption("double_val","300");


    std::cout << "double_val:" << FLAGS_double_val << std::endl;
    std::cout << "bool_val:" << FLAGS_bool_val << std::endl;
    std::cout << "string_val:" << FLAGS_string_val << std::endl;
    return 0;
}

```


### 从配置文件读入参数
```bash
./main  -flagfile=./param.conf
```
>param.conf
```
#注意:""也会被转义
--double_val=1231.4
--bool_val=false
--string_val=lee peng cheng
```

### 从环境变量读入参数
>需要设定环境变量`export FLAGS_double_val=3.141592653`(注意是FLAGS_double_val,不是double_val)
```bash
#从环境变量中读入double_val和string_val的值

./main  -fromenv=double_val,string_val  #如果环境中不存在此变量则直接退出
./main  -tryfromenv=double_val,string_val  #如果环境中不存在此变量则使用默认值
```