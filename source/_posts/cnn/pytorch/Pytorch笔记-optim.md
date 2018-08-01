---
title: Pytorch笔记-optim
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---

#### SGD(梯度下降法)
>optim.SGD(params, lr=required, momentum=0, dampening=0,weight_decay=0, nesterov=False)

* weight decay（权值衰减）的使用既不是为了提高收敛精确度也不是为了提高收敛速度，其最终目的是防止过拟合。   
在损失函数中，weight decay是放在正则项（regularization）前面的一个系数，正则项一般指示模型的复杂度，  
所以weight decay的作用是调节模型复杂度对损失函数的影响，若weight decay很大，则复杂的模型损失函数的值也就大。    
* momentum是梯度下降法中一种常用的加速技术。对于一般的SGD,沿负梯度方向下降，其表达式为,      

$$ x \leftarrow x-\alpha*dx $$

而带momentum项的SGD则写生如下形式:     

$$ v =\beta*v-\alpha*dx $$    
$$ x \leftarrow x+v $$    

其中即momentum系数，通俗的理解上面式子就是，如果上一次的momentum（即）与这一次的负梯度方向是相同的，那这次下降的幅度就会加大，    
所以这样做能够达到加速收敛的过程。    


Implements stochastic gradient descent (optionally with momentum).

note::
The implementation of SGD with Momentum/Nesterov subtly differs from
Sutskever et. al. and implementations in some other frameworks.

Considering the specific case of Momentum, the update can be written as        

$$ v = \rho * v + g $$
$$ p = p - lr * v$$

where p, g, v and : $\rho$ denote the parameters, gradient,
velocity, and momentum respectively.

This is in contrast to Sutskever et. al. and
other frameworks which employ an update of the form

$$ v = \rho * v + lr * g $$
$$ p = p - v $$

```python
Args:
    params :网络参数,一般输入net.parameters()
    lr: 学习速率,一般取1e-3
    momentum: 势能系数,exp:0.9 (default: 0)
    weight_decay: 权重衰减系数(L2惩罚),exp:5e-4 (default: 0)
    dampening (float, optional): dampening for momentum (default: 0)
    nesterov (bool, optional): enables Nesterov momentum (default: False)

Example:
    >>> optimizer = torch.optim.SGD(model.parameters(), lr=0.1, momentum=0.9)
    >>> optimizer.zero_grad()
    >>> loss_fn(model(input), target).backward()
    >>> optimizer.step()
```


