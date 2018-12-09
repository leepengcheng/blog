---
title: latex 常用命令
categories: others
tags:
  - docker
date: 2018-07-15 16:45:52
---

#### 罗马字符
$\sigma$                $\alpha$  
$\beta$                 $\gamma$  
$\delta$                $\epsilon$  
$\zeta$                   $\mu$  
$\lambda$                $\pi$  
$\tau$               $\theta$  
$\omega$               $\xi$


https://zhuanlan.zhihu.com/p/37916911

>$\delta ^ { L } = \nabla _ { a } C \odot \sigma ^ { \prime } \left( z ^ { L } \right)$

如果：$Y = \sigma ( X )$，那么
$\frac { \partial C } { \partial X } = \frac { \partial C } { \partial Y } \odot \sigma ^ { \prime } ( X )$，其中
$\sigma(x)是激活函数$
----


>$\delta ^ { l } = \left( \left( W ^ { l + 1 } \right) ^ { T } \delta ^ { l + 1 } \right) \odot \sigma ^ { \prime } \left( z ^ { l } \right)$


>$\frac { \partial C } { \partial b _ { j } ^ { l } } = \delta _ { j } ^ { l }$


>$\frac { \partial C } { \partial w _ { j k } ^ { l } } = a _ { k } ^ { l - 1 } \delta _ { j } ^ { l }$



神经网络就是把 Y=W\cdot X_l+B，X_{l+1}=f(Y) 反复套用的过程。其中 f 为激活函数。

在最后，损失函数 Lost=C(X_L) ， l 是其中某一层， L 神经网络层数（最后一层），即 l 的最大值。

### 总结
神经网络就是把 $Y=W\cdot X_l+B，X_{l+1}=f(Y)$ 反复套用的过程。其中 `f `为激活函数。

在最后，损失函数 $Loss=C(X_L)$ ，`l` 是其中某一层， L 神经网络层数（最后一层），即 `l` 的最大值。
反向传播：
* (1)首先计算 $\frac{\partial C}{\partial X_L}$
* (2)计算 $\frac{\partial C}{\partial Y}$
* (3)计算$\frac{\partial C}{\partial W}，\frac{\partial C}{\partial X_l}，\frac{\partial C}{\partial B}$ 

(2)--(3)--(2)-(3)--.反复循环，直到输入层

(1)计算 $\frac{\partial C}{\partial X_L}$



