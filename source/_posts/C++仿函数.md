---
title: C++仿函数
categories: programming
tags:
  - c++
  - 仿函数
date: 2017-09-26 22:18:14
---

C++仿函数

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