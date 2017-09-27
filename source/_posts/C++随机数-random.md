---
title: C++随机库-random
categories: programming
tags:
  - c++
  - 随机数
date: 2017-09-26 22:18:14
---

>随机数由生成器(generator)和分布器(distributions)结合产生

#### 生成器
>能够产生离散的等可能分布数值,需要注意的是对于1个给定的生成器，每次程序运行都会生成相同的序列。    
例如下面的函数,目标是生成填充100个随机数,但是其实每次返回的值都相等。
```c++
vector<int> bad_gen_randInts()
{
  vector<int> vecs;
  default_random_engine e;
  uniform_int_distribution<> u(0,100);
  for(int i=0;i<100;++i>)
  {
    vecs.push_back(u(e));
  }
  return vecs;
}
```
解决这一问题的正确方法是把引擎和分布定义为static的，这样他们的状态才会保持。第一次调用会使用   
u(e)生成的前100个数，第二次会获得接下来的100个数，依次类推。 还可以用`rand_device`生成随机   
种子填充引擎，例如`default_random_engine(rand_device()())`。
```c++  
vector<int> good_gen_randInts()
{
  vector<int> vecs;
  static default_random_engine e;
  static uniform_int_distribution<> u(0,100);
  for(int i=0;i<100;++i>)
  {
    vecs.push_back(u(e));
  }
  return vecs;
}

* `random_device`:真随机数生成器或叫f非确定性随机数生成器,linux中有效，windows下其实也是伪随机    
* `default_random_engine`:伪随机生成器，windows默认为`mt19937`生成器，其他伪随机生成器见下    
* `minstd_rand`: Minimal Standard minstd_rand generator 
* `minstd_rand0`: Minimal Standard minstd_rand0 generator 
* `mt19937`: Mersenne Twister 19937 generator 
* `mt19937_64`: Mersene Twister 19937 generator (64 bit)
* `ranlux24_base`:Ranlux 24 base generator 
* `ranlux48_base`: Ranlux 48 base generator 
* `ranlux24`: Ranlux 24 generator 
* `ranlux48`: Ranlux 48 generator 
* `knuth_b`: Knuth-B generator 

####  分布器
>能够把生成器产生的均匀分布值映射到其他常见分布，下面仅列出部分分布器类型
* 均匀分布:  `uniform_int_distribution`,`uniform_real_distribution`
* 正态分布: `normal_distribution`
* 二项分布: `binomial_distribution`
* 离散分布: `discrete_distribution`
* 泊松分布: `poisson_distribution`
* 努伯利分布： `bernoulli_distribution `
* 其他分布: `gamma_distribution`

<!-- more -->

#### 使用方法
```c++
#include <random>
#include <iostream>
using namespace std;

//用random_device生成随机种子初始化defaut_random_engine
std::random_device rd;
std::default_random_engine generator(rd());

//也可直接初始化
std::default_random_engine generator;

//int型均匀分布
//可以使用std::uniform_int_distribution<> dis_u_i(1,6);
std::uniform_int_distribution<int> dis_u_i(1,6);
//参数是generator,不是generator()
int rand_int = dis_u_i(generator);  // 生成均匀1-6的均匀int分布

//double型均匀分布
std::uniform_real_distribution<double> dis_u_r(0.0,1.0)
double rand_double = dis_u_r(generator);  // 生成均匀0.0-1.0的double分布


//离散分布，int型
std::discrete_distribution<int> dis_dis({ 1, 2, 3, 4, 5 });  
cout << "min() == " << dis_dis.min() << endl; //最小值0
cout << "max() == " << dis_dis.max() << endl; //最大值4
vector<double> prob = dis_dis.probabilities(); //每个的得概率
int rand_dis=dis_dis(generator);//离散分布产生的下个值0~4

//正态分布(均值和标准差)
std::normal_distribution<double> dis_norm(10, 2);  
cout << "min() == " << dis_norm.min() << endl;  
cout << "max() == " << dis_norm.max() << endl;  
cout << "m() == "  << dis_norm.mean() << endl;  
cout << "s() == " << dis_norm.stddev() << endl; 
//下次生成正态分布的值
double rand_norm=dis_norm(generator);


//伯努利分布(bool值)
std::bernoulli_distribution dis_bool(0.5);
//为true概率值
cout<<dis_bool.p<<endl;
bool rand_bool=dis_bool(generator);



//二次分布(次数和概率)
//example:已知硬币正面的概率为0.5，扔10次后有n次（n=(1到10)）为正面的概率
std::binomial_distribution<int> dis_bin(10, 0.5);  
std::cout << std::endl;  
std::cout << "p == " << dis_bin.p() << std::endl;  
std::cout << "t == " << dis_bin.t() << std::endl;
for(int i=0;i<10;++i)
{
  //出现正面的次数0-10
  cout<<dis_norm(generator)<<endl;
}

//为了复用,可以使用std::bind
auto dice = std::bind (dis_u_i, generator );
int wisdom = dice()+dice()+dice();
```


#### shuffle和random_shuffle
这两个函数位均位于于 `<algorithm>` 头文件之下。   
* `random_shuffle`算法在C++11之前就已经存在，C++11之后由于右值引用的引入，它的使用范围变大了。   
**该函数在c++14已弃用**，因为其采用了c函数，而且该函数采用全局状态的种子。`shuffle`的生成器采用`<random>`,    
不会保存全局状态，而且可以使用生成器和分布(瞎比翻译请见谅)。

```
std::random_shuffle may make use, under the hood, of random C family of functions.     
These functions use global state for seeds and other state.

So it is being deprecated because shuffle will do the same, but better.    
Namely, it uses the new <random> header from C++11 that doesn't use global state,
but its own objects making use of generators, devices and distributions. 
```   

* `shuffle`算法则是从C++11之后才开始出现，可以与随机数和分布库一起使用。
```c++
#include <vector>
#include <algorithm>
#include <random>
#include <iostream>
using namespace std;

//自定义随机生成器
//class SelfGenerator
//{
//public:
    //typedef ptrdiff_t long long
//    ptrdiff_t operator() (ptrdiff_t max)
//    {
//        double temp;
//        temp = static_cast<double>(rand()) / static_cast<double>(RAND_MAX);
//        return static_cast<ptrdiff_t>(temp * max);
//   }
//};
//int myrandom (int i) { return std::rand()%i;}

int main()
{
//数组
int nums[]={ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
int n=sizeof(nums)/sizeof(nums[0]);

//向量
vector<int, 10> a(nums,nums+n);

//默认生成器
default_random_engine defaultEngine;    
shuffle(a.begin(), a.end(), defaultEngine);
//随机种子生成器
random_device rd;
shuffle(a.begin(), a.end(), defaultEngine(rd()));

//对数组随机排序
shuffle(nums, nums+n, defaultEngine);

//自定义随机函数，random_shuffle在c++14已弃用
//random_shuffle(a.begin(), a.end());     // use default rand()
//SelfGenerator sg;//伪函数
//random_shuffle(a.begin(), a.end(), sg);
//random_shuffle(a.begin(), a.end(), myrandom);
}

```