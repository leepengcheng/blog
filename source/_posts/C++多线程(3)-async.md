---
title: C++多线程之异步线程
categories: programming
tags:
  - c++
  - 多线程
date: 2017-09-25 00:07:40
---

C++多线程之异步线程
<!-- more -->
```c++
#include <iostream>
#include <thread>
int main(int argc, char *argv[])
{
    
    auto fun=[](const char* data,int number){
        std::this_thread::sleep_for(std::chrono::seconds(1));
        for(int i=0;i<10;++i)
        {
            std::cout<<data<<number<<std::endl;
        }
    };
    std::thread t1([](){
        std::this_thread::sleep_for(std::chrono::seconds(1));
        for(int i=0;i<10;++i)
        {
            std::cout<<"hello:"<<std::this_thread::get_id()<<std::endl;
        }
    });
    std::thread t2([](){
        std::this_thread::sleep_for(std::chrono::seconds(1));
        for(int i=0;i<10;++i)
        {
            std::cout<<"world"<<std::this_thread::get_id()<<std::endl;
        }
    });

    //输出线程id和cpu核数
    std::cout << "ID:" << t1.get_id() << std::endl;
    std::cout << "CPU:" << std::thread::hardware_concurrency() << std::endl;
    std::thread t3(fun,"fuck",123);
//    t1.join();
    t1.detach();
//    t2.join();
    t2.detach();
//    t3.join();
    t3.detach();

    std::cout<<"main finished"<<std::endl;

    return 0;
}
```