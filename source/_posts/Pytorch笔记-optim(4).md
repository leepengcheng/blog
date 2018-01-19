---
title: Pytorch笔记-optim(4)
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---

* SGD(梯度下降法)
```python
optim.SGD(params, lr=required, momentum=0, dampening=0,weight_decay=0, nesterov=False)
"""Implements stochastic gradient descent (optionally with momentum).

Nesterov momentum is based on the formula from
`On the importance of initialization and momentum in deep learning`__.

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
    note::
    The implementation of SGD with Momentum/Nesterov subtly differs from
    Sutskever et. al. and implementations in some other frameworks.

    Considering the specific case of Momentum, the update can be written as

    math::
              v = \rho * v + g \\
              p = p - lr * v

    where p, g, v and :math:`\rho` denote the parameters, gradient,
    velocity, and momentum respectively.

    This is in contrast to Sutskever et. al. and
    other frameworks which employ an update of the form

    math::
            v = \rho * v + lr * g \\
            p = p - v

    The Nesterov version is analogously modified.
"""
```