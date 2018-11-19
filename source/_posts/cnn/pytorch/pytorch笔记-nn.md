---
title: pytorch笔记-nn
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---

#### nn.BatchNorm*d
n.BatchNorm*d的是指在神经网络中激活函数的前面，    
将按照特征进行normalization，这样做的好处有3点：    
* 1、提高梯度在网络中的流动。Normalization能够使特征全部缩放到[0,1],这样在反向传播时候的梯度都是在1左右，避免了梯度消失现象。   
* 2、提升学习速率。归一化后的数据能够快速的达到收敛。   
* 3、减少模型训练对初始化的依赖。


#### nn.Embedding
num_embeddings (int): 向量的数目(batch_size?)   
embedding_dim (int): 每个向量的大小   
padding_idx (int, optional):遇到此下标时填充0   
max_norm (float, optional): 如果提供则会归一化输入，使其范数小于该值   
norm_type (float, optional): 为`max_norm`选项提供p范数的p   
scale_grad_by_freq (bool, optional): 根据向量中单词中的频率缩放梯度  
sparse (bool, optional): if ``True``, gradient w.r.t. weight matrix will be a sparse tensor. See Notes for
                            more details regarding sparse gradients.

```python
#相当于size=10的字典,每个变量由3个向量组成
#根据input的值自动生成对应的向量值
#例如input1中的1,对应向量[ 0.3890, -0.1115, -0.5363]
embed1=nn.Embedding(10,3)
input=torch.LongTensor([[1,2,3,4],[4,3,2,1]])
#注意要求输入LongTensor
output1=embed1(input)

#tensor:shape=(2,4,3)
# tensor([[[ 0.3890, -0.1115, -0.5363],
#          [-0.1610,  0.3988, -0.9604],
#          [-1.1017,  0.3128, -1.3697],
#          [ 1.1119, -1.2120, -0.5004]],

#         [[ 1.1119, -1.2120, -0.5004],
#          [-1.1017,  0.3128, -1.3697],
#          [-0.1610,  0.3988, -0.9604],
#          [ 0.3890, -0.1115, -0.5363]]])
embed2=nn.Embedding(10,3,padding_idx=1)
output2=embed2(input)

# tensor([[[ 0.0000,  0.0000,  0.0000],
#          [-0.6880, -0.9794,  3.2113],
#          [ 2.1777, -1.1724, -0.3580],
#          [-0.8459, -0.8717, -1.5739]],

#         [[-0.8459, -0.8717, -1.5739],
#          [ 2.1777, -1.1724, -0.3580],
#          [-0.6880, -0.9794,  3.2113],
#          [ 0.0000,  0.0000,  0.0000]]])

```


### nn.MaxPool2d

```python
ceil_mode主要用于图片边缘特征的处理,根据image_size%kernel_size的值会影响feature_map的大小
当image_size%kernel_size==0时,ceil_mode=True不起作用
当image_size%kernel_size!=0时,ceil_mode=True会将不足的区域设为-Nan
x=torch.randn(1,1,5,5)
# tensor([[[[-0.7191, -1.5240, -0.8989, -0.5512,  0.0420],
#           [-0.1819,  0.9954, -0.5986,  1.7122,  0.2929],
#           [-2.3068, -1.2146,  0.1682,  0.4681, -0.9131],
#           [-0.9621,  0.2287, -0.4446, -0.4750, -0.4182],
#           [-0.2618, -0.1063, -0.0056, -0.1310,  0.1979]]]])
pool_1=torch.nn.MaxPool2d(kernel_size=2,ceil_mode=False)
pool_1(x)
# tensor([[[[0.9954, 1.7122],
#           [0.2287, 0.4681]]]])           size=[1,1,2,2]
pool_2=torch.nn.MaxPool2d(kernel_size=2,ceil_mode=True)
pool_2(x)
# tensor([[[[ 0.9954,  1.7122,  0.2929],
#           [ 0.2287,  0.4681, -0.4182],
#           [-0.1063, -0.0056,  0.1979]]]]) size=[1,1,3,3]

```