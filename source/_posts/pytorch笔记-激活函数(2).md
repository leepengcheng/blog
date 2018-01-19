---
title: pytorch笔记-激活函数(2)
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---

### nn.Relu
>inplace=True时直接替换输入作为输出,可以稍微降低内存.  
但是有时候无法使用,因为输入可能会被释放,如报错需改成False
```python

nn.Relu(inplace=True)
```


