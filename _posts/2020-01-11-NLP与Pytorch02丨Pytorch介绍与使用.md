---
layout: post
title: NLP与Pytorch02丨Pytorch介绍与使用
tag: NLP
---

## 一.Pytorch安装

    pip3 install torch torchvision torchaudio

![20211216173746](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211216173746.png)

在ipython中查看pytorch版本

![20211216174630](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211216174630.png)

## 二.张量Tensor介绍与使用

### 1.什么是张量

张量是一个统称，其中包含很多类型：

- 0阶张量：标量、常数scaler

- 1阶张量：向量vector

- 2阶张量：矩阵natrix

- 3阶张量. . .

**阶**指的是一个数组里边形状的个数

### 2.pytorch中创建张量

#### 2.1使用python中的列表或者序列创建tensor

    import torch

    a1 = torch.Tensor([1, 2, 3])
    print(a1)

#### 2.2使用numpy中的数组创建tensor

    array1 = torch.Tensor(np.arange(12).reshape(3, 4))
    print(array1)

#### 2.3使用torch的api创建tensor

    import torch

    # 创建3行4列全为空的tensor
    print(torch.empty(3, 4))
    # 创建3行4列全为1的tensor
    print(torch.ones(3, 4))
    # 创建3行4列全为0的tensor
    print(torch.zeros(3, 4))
    # 创建3行4列随机值的tensor,随机值区间为[0,1)
    print(torch.rand(3, 4))
    # 创建3行4列的随机整数的tensor,随机值的区间是0-10
    print(torch.randint(low=0, high=10, size=[3, 4]))
    print(torch.randint(0, 10, (3, 4)))


