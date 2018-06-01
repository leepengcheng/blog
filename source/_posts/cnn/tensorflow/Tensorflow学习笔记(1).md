---
title: Tensorflow学习笔记(1)
categories: ml 
tags:
  - tensorflow
date: 2017-01-02 15:59:19
---

* 学习率不是越小越好，下列例子中0.5的学习率比0.1更快且更容易收敛
* 下述程序属于线性模型，不能使用交叉熵的损失函数，否则无法收敛
<!--more -->
```python
#coding:utf-8
import tensorflow as tf
'''
已知方程 xW+B=y
给出100个x输入和对应的输出y
求W和b的拟合值
即W->[0.100, 0.200] b->0.300的偏离程度
'''
# 生成随机数据总共 100 个点的输入值和输出值
x_data = np.float32(np.random.rand(100, 2)) 
y_data = np.dot(x_data,[[0.1],[0.2]]) + 0.300
W = tf.Variable(tf.random_uniform([2, 1], -1.0, 1.0))
B = tf.Variable(tf.zeros([1]))
y = tf.matmul(x_data,W) + B# 构造线性模型 x*W+b=y
loss = tf.reduce_mean(tf.square(y - y_data))# 损失函数->均方差
# loss=tf.reduce_mean(-y_data*tf.log(y))# 损失函数->交叉熵
train  = tf.train.GradientDescentOptimizer(0.5).minimize(loss)#学习率
init = tf.global_variables_initializer()# 初始化变量
with tf.Session() as sess:
  sess.run(init)
  for step in range(100):
      l,_,w,b=sess.run([loss,train,W,B])
      print("step: %4d-loss:%s-w:%s-b:%s\n"%(step,l,w,b))
```