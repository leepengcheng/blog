---
title: Pytorch笔记-torchvision(3)
categories: ml 
tags:
  - pytorch
date: 2017-01-02 15:59:19
---


* DataLoader
```python
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None, batch_sampler=None,
                num_workers=0, collate_fn=default_collate, pin_memory=False, drop_last=False):
参数:
    dataset (Dataset): dataset from which to load the data.
    batch_size (int, optional): 每批次的样本数目(default: 1).
    shuffle (bool, optional): 每个epoch都会打乱样本顺序(default: False).
    sampler (Sampler, optional): defines the strategy to draw samples from
        the dataset.如果指定了该参数, ``shuffle`` 必须为False.
    batch_sampler (Sampler, optional): like sampler, but returns a batch of
        indices at a time. Mutually exclusive with batch_size, shuffle,
        sampler, and drop_last.
    num_workers (int, optional): 加载数据时使用的进程数目. 0表示只会加载到主线程(default: 0)
    collate_fn (callable, optional): merges a list of samples to form a mini-batch.
    pin_memory (bool, optional): If ``True``, 在返回Tensor之前回将其复制到GPU的固定(页锁)内存中.
          此时主机到GPU的复制速度更快,同时可以使用异步的GPU副本,只需要传参数async=True到cuda()
    drop_last (bool, optional): set to ``True`` to drop the last incomplete batch,
        if the dataset size is not divisible by the batch size. If ``False`` and
        the size of dataset is not divisible by the batch size, then the last batch
        will be smaller. (default: False)
```