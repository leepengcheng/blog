---
title: Pytorch笔记-Tensor
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---

#### torch.gather
```python
torch.gather(input, dim, index, out=None) → Tensor
#按照制定轴dim获得数据,等价于tensor.gather(dim,index)
#index 为LongthTensor不是FloatTensor
#N=index[i][j][k]
#out[i][j][k] = input[N][j][k]  # if dim == 0
#out[i][j][k] = input[i][N][k]  # if dim == 1
#out[i][j][k] = input[i][j][N]  # if dim == 2

>>> t = torch.Tensor([[1,2],[3,4]])
>>> torch.gather(t, 1, torch.LongTensor([[0,0],[1,0]]))
#index和input的batch_size一样(行数目一致))
#[1,2]的index[0,0]为[1,1]
#[3,4]的index[1,0]为[4,3]
#输出：
#1  1  
#4  3 
```

#### torch.clamp
```python
torch.clamp(input, min, max, out=None) → Tensor
#将tensor限制在[min, max]范围内，等价于tensor.clamp(min,max)
#当只有min或者max时则限制最小或者最大值
#      | min, if x_i < min
#y_i = | x_i, if min <= x_i <= max
#      | max, if x_i > max
>>> x=torch.Tensor([1.38690.3912,-0.8634,-0.5468])
>>> torch.clamp(x,0.5,1),torch.clamp(x,min=0.5),torch.clamp(x,max=1)
```

-------
**(提示：pytorch 0.4已经废除Variable及volatile关键字)**   
Variable的`requires_grad`与`volatile`参数:    
在创建一个Variable是，有两个bool型参数可供选择，一个是`requires_grad`，一个是`Volatile`
* `requires_grad`不是十分对该Variable进行计算梯度，一般在finetune是可以用来固定某些层的参数，减少计算。
只要有一个叶节点是True，其后续的节点都是True
* `volatile=True`，一般用在训练好网络，只进行inference操作时使用，其不建立Variable与Function的关系。
只要有一个叶子节点是True，其后节点都是True
--------

#### torch.nn.Conv2d
```python
class torch.nn.Conv2d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True)

二维卷积层, 输入的尺度是(N, C_in,H,W)，输出尺度（N,C_out,H_out,W_out）的计算方式：

说明
bigotimes: 表示二维的相关系数计算 stride: 控制相关系数的计算步长
dilation: 用于控制内核点之间的距离，详细描述在这里
groups: 控制输入和输出之间的连接： group=1，输出是所有的输入的卷积；group=2，此时相当于有并排的两个卷积层，
每个卷积层计算输入通道的一半，并且产生的输出是输出通道的一半，随后将这两个输出连接起来。

参数kernel_size，stride,padding，dilation也可以是一个int的数据，此时卷积height和width值相同;
也可以是一个tuple数组，tuple的第一维度表示height的数值，tuple的第二维度表示width的数值

参数：
in_channels(int) – 输入信号的通道
out_channels(int) – 卷积产生的通道
kerner_size(int or tuple) - 卷积核的尺寸
stride(int or tuple, optional) - 卷积步长
padding(int or tuple, optional) - 输入的每一条边补充0的层数
dilation(int or tuple, optional) – 卷积核元素之间的间距
groups(int, optional) – 从输入通道到输出通道的阻塞连接数
bias(bool, optional) - 如果bias=True，添加偏置
```