---
title: C++时间库chrono
categories: programming
tags:
  - c++
date: 2017-03-07 09:30:22
---

传统ctime计时方法,该方法的计时精度为1毫秒    
```c++
#include <ctime>
using namespace std;
clock_t start = clock();
// do something...
clock_t end   = clock();
cout << "Cost" << (double)(end - start) / CLOCKS_PER_SEC << "Second" << endl;
```
<!-- more -->
---

<!-- ################################################ -->
Chrono[kroʊnoʊ],翻译为`慢性的长期的`,是c++ 11中的时间库，提供计时，时钟等功能,该方法的计时精度为1微秒。     
学习chrono，关键是理解里面时间段(`Durations`)、时间点(`Time points`)的概念。    
##### 时钟节拍(ratio)：
>`template <intmax_t N, intmax_t D = 1> class ratio`    

其中N表示分子，D表示分母，默认用秒表示的时间单位。     
N对应于其成员num，D对应于其成员den,常用的单位：    
```c++
ratio<60, 1>                    minute
ratio<1, 1>                      second
ratio<1, 1000>               microsecond
```
---
##### 时间段(duration)
>`template <class Rep, class Period = ratio<1>>`    
`class duration`   

`std::chrono::duration` 表示一段时间，比如两个小时，12.88秒，半个时辰，一炷香的时间等等    
`Rep`表示一种数值类型，用来表示`Period`的数量，比如`int float double`。    
`Period`是`ratio`类型，用来表示上面所说的单位精度，比如`second milisecond`。    
`chrono`中宏定义了许多特例化了的`duration`：    
就是常见的`hours，miniutes，seconds，milliseconds`等，使用`std::chrono::millisecond`s直接使用。    
* 构造函数: 
```c++
duration() = default;    //默认构造  
duration (const duration& dtn);        //(2)(3)拷贝构造  
template<class Rep2, class Period2>  
   constexpr duration (const duration<Rep2,Period2>& dtn);  
template<class Rep2>      //传递一个某类型（int等）的数值，构造一个时间段     
   constexpr explicit duration (const Rep2& n);  
```
* 成员函数count()返回单位时间的数量   
```c++
#include <iostream>  
#include <chrono>  
int main()  
{  
    std::chrono::milliseconds mscond(1000); // 1 second  
    std::cout << mscond.count() << " milliseconds.\n";  
  
    std::cout << mscond.count() * std::chrono::milliseconds::period::num /std::chrono::milliseconds::period::den;  
    std::cout << " seconds.\n";  
    cin.get();//暂停
    //system("pause"); //不可移植、占用资源，慎用  
    return 0;  
} 
```
#### 时间点(time_point)
`template <class Clock, class Duration = typename Clock::duration>`    
`class time_point;`    
`std::chrono::time_point` 表示一个具体时间，如上个世纪80年代、今天下午3点、火车出发时间等，只要它能用计算机时钟表示。      
第一个模板参数Clock用来指定所要使用的时钟（标准库中有三种时钟，`system_clock`，`steady_clock`和`high_resolution_clock`），      
第二个模板函数参数用来表示时间的计量单位(特化的`std::chrono::duration<>` ),时间点都有一个时间戳，即时间原点。    
chrono库中采用的是Unix的时间戳1970年1月1日 00:00。所以`time_point`也就是距离时间戳(`epoch`)的时间长度（`duration`）。    
* 构造函数：
```c++
time_point();           //默认构造函数，时间戳作为其值
template <class Duration2>
time_point (const time_point<clock,Duration2>& tp);  //拷贝构造函数
explicit time_point (const duration& dtn);  //使用duration构造，就是距离时间戳的时间长度
```
时间点有个重要的函数：`duration time_since_epoch()`  用于获取当前时间点距离时间戳的时间长度       
即经常用来得到当前时间点到1970年1月1日00:00的时间距离。    
该函数返回的`duration`的精度和构造`time_point`的时钟(Clock)有关。      
```c++
#include <iostream>  
#include <chrono>  
#include <ctime>  
using namespace std;  
int main()  
{  
    //距离时间戳2两秒  
    chrono::time_point<chrono::system_clock, chrono::seconds> tp(chrono::seconds(2));  
    cout << "to epoch : " <<tp.time_since_epoch().count() << "s" <<endl;  
    //转化为ctime，打印输出时间点  
    time_t tt = chrono::system_clock::to_time_t(tp);  
    char a[50];  
    ctime_s(a, sizeof(a), &tt);  
    cout << a;  
    system("pause");  
    return 0;  
} 
```
#### 时钟(当前系统的时间)
chrono中有三种时钟：`system_clock`，`steady_clock`和`high_resolution_clock`。每一个clock类中都有确定的    
`time_point`, `duration`, `Rep`, `Period`类型。`system_clock`是不稳定的。因为时钟是可调的，    
即这种是完全自动适应本地账户的调节。这种调节可能造成的是，首次调用`now()`返回的时间要早于上次调用`now()`所返回的时间，        
这就违反了节拍频率的均匀分布。稳定闹钟对于超时的计算很重要，所以C++标准库提供一个稳定时钟 `std::chrono::steady_clock`。   `std::chrono::high_resolution_clock` 是标准库中提供的具有最小节拍周期(因此具有最高的精度的时钟)。    
上文所说`time_since_epoch()`，以及将要介绍的`now(`)函数的返回值都依赖于时钟的精度，测试时钟的精度的一种方法就是：    
```c++
#include <iostream>  
#include <chrono>  
using namespace std;  
int main()  
{  
    cout << "system clock          : ";  
    cout << chrono::system_clock::period::num << "/" << chrono::system_clock::period::den << "s" << endl;  
    cout << "steady clock          : ";  
    cout << chrono::steady_clock::period::num << "/" << chrono::steady_clock::period::den << "s" << endl;  
    cout << "high resolution clock : ";  
    cout << chrono::high_resolution_clock::period::num << "/" << chrono::high_resolution_clock::period::den << "s" << endl;  
    system("pause");  
    return 0;  
}
```
windows系统的测试结果是`system_clock`的精度是100纳秒，而`high_resolution`的精度是1纳秒，    
对于程序来说，一般毫秒级就够了，所以说chrono提供的时钟精度绰绰有余。    
```c++
static time_point now() noexcept; //成员函数用于获取系统的当前时间。   
//由于各种time_point表示方式不同，chrono也提供了相应的转换函数 time_point_cast。
template <class ToDuration, class Clock, class Duration>
time_point<Clock,ToDuration> time_point_cast (const time_point<Clock,Duration>& tp);
//传一个要转换为的精度的duration模板参数和一个要转换的time_point参数
//其他成员函数：
to_time_t() // time_point转换成time_t秒
from_time_t() //从time_t转换成time_point
```

* 综合应用
```c++
#include <iostream>  
#include <chrono>  
#include <ctime>  
using namespace std;  
int main()  
{  
    //定义毫秒级别的时钟类型  
    typedef chrono::time_point<chrono::system_clock, chrono::milliseconds> microClock_type;  
    //获取当前时间点，windows system_clock是100纳秒级别的(不同系统不一样)，所以要转换  
    microClock_type tp = chrono::time_point_cast<chrono::milliseconds>(chrono::system_clock::now());  
    //转换为ctime.用于打印显示时间  
    time_t tt = chrono::system_clock::to_time_t(tp);  
    char _time[50];  
    ctime_s(_time,sizeof(_time),&tt);  
    cout << "now time is : " << _time;  
    //计算距离1970-1-1,00:00的时间长度，因为当前时间点定义的精度为毫秒，所以输出的是毫秒  
    cout << "to 1970-1-1,00:00  " << tp.time_since_epoch().count() << "ms" << endl;  
    system("pause");  
    return 0;  
}  
```
* 计时函数      
```c++
#include <chrono>   
using namespace std;
using namespace chrono;

auto start = steady_clock::now();
// do something...
auto end   = steady_clock::now();
auto duration = duration_cast<microseconds>(end - start);
//微秒，由`<microseconds>`决定
cout<<duration.count()<<endl;
//转换为秒,带小数位
cout<<double(duration.count()) * microseconds::period::num / microseconds::period::den<<endl;


```

* 延迟函数   
```c++
#include <chrono>  
#include <thread>
//延迟3毫秒和3秒
std::this_thread::sleep_for(std::chrono::milliseconds(3));
std::this_thread::sleep_for(std::chrono::seconds(3));
```
