---
title: pytorch之Tensor
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---


```python
torch.gather(input, dim, index, out=None) → Tensor
#按照制定轴dim获得数据,等价于tensor.gather(dim,index)
#index 为LongthTensor不是FloatTensor
#N=index[i][j][k]
#out[i][j][k] = input[N][j][k]  # if dim == 0
#out[i][j][k] = input[i][N][k]  # if dim == 1
#out[i][j][k] = input[i][j][N]  # if dim == 2

```
>>> t = torch.Tensor([[1,2],[3,4]])
>>> torch.gather(t, 1, torch.LongTensor([[0,0],[1,0]]))
>>> 1  1  #对于x[0][1],由于index[0][1]=0,dim=1则
>>> 4  3  #则x=t[0][index[0][1]]=t[0][0]=1
```
```



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