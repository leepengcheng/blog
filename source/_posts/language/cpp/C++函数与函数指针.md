---
title: C++仿函数与函数指针
categories: programming
tags:
  - c++
  - 仿函数
  - 函数指针
date: 2017-09-26 22:18:14
---

#### C++仿函数
仿函数其实本质上就是重载和`()`操作符的类

<!-- more -->

```c++
#include <iostream>
#include <algorithm>
#include <vector>

class CMP
{
private:
  const int n;

public:
  //int &m 报错？
  CMP(const int &n) : n(n) {}
  bool operator()(const int &m) const
  {
    return m <= n;
  }
  static bool cmp1(const int &a, const int &b)
  {
    return a <= b;
  }
  bool cmp2(const int &a, const int &b) const
  {
    return a <= b;
  }
};

bool cmp(int &m, int &n)
{
  return m <= n;
}

int main(int argc, char *argv[])
{
  std::vector<int> vecs = {1, 2, 3, 4, 5, 6, 7, 8, 9};

  //绑定全局函数
  auto fcmp1 = std::bind(cmp, std::placeholders::_1, 8);
  int res1 = std::count_if(vecs.begin(), vecs.end(), fcmp1);

  //绑定仿函数
  CMP fcmp2(8);
  int res2 = std::count_if(vecs.begin(), vecs.end(), fcmp2);

  //绑定类成员函数
  auto fcmp3 = std::bind(&CMP::cmp2, &fcmp2, std::placeholders::_1, 8);
  int res3 = std::count_if(vecs.begin(), vecs.end(), fcmp3);

  //绑定类静态函数
  auto fcmp4 = std::bind(&CMP::cmp1, std::placeholders::_1, 8);
  //返回类型为function<bool(const int&)>
  // std::function<bool(const int&)> fcmp4=std::bind(&CMP::cmp1,std::placeholders::_1,8);
  int res4 = std::count_if(vecs.begin(), vecs.end(), fcmp4);

  return 0;
}

```
#### 函数指针
`typedef int(*Fun)(char*) ==using Fun=int(*)(char*)`




#### std::function

```c++
#include <iostream>
#include <map>
#include <functional>
using namespace std;
 
// 普通函数
int add(int i, int j) { return i + j; }
// lambda表达式
auto mod = [](int i, int j){return i % j; };
// 函数对象类
struct divide
{
	int operator() (int denominator, int divisor)
	{
		return denominator / divisor;
	}
};
 
///////////////////////////SubMain//////////////////////////////////
int main(int argc, char *argv[])
{
	// 受限的map
	map<char, int(*)(int, int)> binops_limit;
	binops_limit.insert({ '+', add });
	binops_limit.insert({ '%', mod });
	// 错误	1	error C2664: “void std::_Tree<std::_Tmap_traits<_Kty,_Ty,_Pr,_Alloc,false>>::insert(std::initializer_list<std::pair<const _Kty,_Ty>>)”: 无法将参数 1 从“initializer-list”转换为“std::pair<const _Kty,_Ty> &&”
	// binops_limit.insert({ '%', divide() });
 
	// 更灵活的map
	map<char, function<int(int, int)>> binops = 
	{
		{ '+', add },
		{ '-', minus<int>() },
		{ '*', [](int i, int j){return i - j; } },
		{ '/', divide() },
		{ '%', mod },
	};
	cout << binops['+'](10, 5) << endl;
	cout << binops['-'](10, 5) << endl;
	cout << binops['*'](10, 5) << endl;
	cout << binops['/'](10, 5) << endl;
	cout << binops['%'](10, 5) << endl;
	system("pause");
	return 0;
}
```
如上所示，function可以将普通函数，lambda表达式和函数对象类统一起来。它们并不是相同的类型，然而通过function模板类，可以转化为相同类型的对象（function对象），从而放入一个map里。

另外我实际测试的结果来看，在VS2013编译器下，上述代码可以通过，而第五版《C++ Primer》第512页第一行所言“错误：mod不是一个函数指针”并没有发生错误，有可能是对C++11标准的不同实现吧。