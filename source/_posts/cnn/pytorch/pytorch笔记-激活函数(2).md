---
title: pytorch笔记-激活函数(2)
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---



![activation functions](../../../assets/Activation_Functions.png "Optional title attribute")


### nn.Relu
>inplace=True时直接替换输入作为输出,可以稍微降低内存.  
但是有时候无法使用,因为输入可能会被释放,如报错需改成False
```python
nn.Relu(inplace=True)
```



### nn.Softmax
针对多个Tensor的归一化(用于多分类))
```python
input = torch.randn(2, 3)
#tensor([[ 1.9144, -1.9266,  0.8993],[-0.0207, -1.1922, -0.6528]])
m = nn.Softmax(0) #每列之和为1
output = m(input)
#tensor([[ 0.8738,  0.3242,  0.8252],[ 0.1262,  0.6758,  0.1748]])

m = nn.Softmax(1) #每行之和为1
output = m(input)
#tensor([[ 0.7226,  0.0155,  0.2619],[ 0.5431,  0.1683,  0.2886]])

```


### nn.Sigmod
针对单个Tensor的映射(用于二分类))
```python
input = torch.randn(2, 3)
#tensor([[ 1.9144, -1.9266,  0.8993],[-0.0207, -1.1922, -0.6528]])

s=nn.Sigmod()
s(input)
#tensor([[ 0.8715,  0.1271,  0.7108],[ 0.4948,  0.2329,  0.3424]])

```

### nn.PReLU
```python
args:
lower: lower bound of the uniform distribution. Default: :math:`\frac{1}{8}`
upper: upper bound of the uniform distribution. Default: :math:`\frac{1}{3}`
inplace: can optionally do the operation in-place. Default: ``False``
```