---
title: C++多线程之thread
categories: programming
tags:
  - c++
  - 多线程
date: 2017-09-25 00:07:40
---




```c++
#include <iostream>
#include <thread>
void foo() 
{
  // do stuff...
}
int main(int argc, char *argv[])
{
    
    //输出主线程id和cpu核数
    std::cout << "Main Thread ID:" <<<std::this_thread::get_id() << std::endl;
    std::cout << "CPU:" << std::thread::hardware_concurrency() << std::endl;

    //不带参数的线程
    std::thread t1(foo);

    //不带参数+lamdba的线程
    std::thread t2([](){
        std::this_thread::sleep_for(std::chrono::seconds(1));
        //子线程ID
        std::cout<<"Thread ID:"<<std::this_thread::get_id()<<std::endl;
    });
    

    //带参数+lambda表达式的线程
    auto func=[](const char* data,int number){
        cout<<"data:"<<data<<"number:"<<number;
    };
    std::thread t3(func,"hello",10086);

    t1.join();//该线程执行完毕前主线程不会停止
    t2.join();//该线程执行完毕前主线程不会停止
    t3.detach();//后台线程，主线程执行完毕后该线程仍在执行

    std::cout<<"main finished"<<std::endl;

    return 0;
}
```