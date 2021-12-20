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

    # 创建3行4列的随机数tensor,随机值的分布式均值为0，方差为1
    print(torch.randn(3, 4))

### 3.张量的方法和属性

#### 3.1 获取tensor中数据（只有一个元素）`tensor.item()`

    t1 = torch.Tensor([[[1]]])
    print(t1.item())   # 1.0

#### 3.2 转换为numpy数组  

    t2 = torch.Tensor([[[1, 2]]])
    print(t2.numpy())  ##[[[1. 2.]]]

#### 3.3 获取形状 tensor.size()

    print(t2.size())  ## torch.Size([1, 1, 2])
    
    #获取第一个维度的值
    print(t2.size(1)) ##  1

#### 3.4 形状改变 tensor.view()

类似numpy中的reshape,是一种浅拷贝，仅仅是形状发生改变

    x = torch.randn(4, 4)
    print(x.size())   # torch.Size([4, 4])
    y = x.view(16)
    print(y.size())   # torch.Size([16])
    # -1表示该维度取决于其他维度的大小，即(4x4)/8=2
    z = x.view(-1, 8)
    print(z.size())  # torch.Size([2, 8])
    # 改变为更多维度
    m = x.view(2, 2, 2, 2)
    print(m.size())   # torch.Size([2, 2, 2, 2])

    # 转换维度为一维，有一种简单的方式，即参数为 -1 
    print(t2.view([-1])) # tensor([3., 5., 8.])

#### 3.5 获取维度 tensor.dim()

    print(m.dim())   # 4

#### 3.6 最大值最小值 tensor.max()

    print(x.max())
    print(x.min())

#### 3.7 转置 

##### tensor.t() 适用于二维

    t5 = torch.randn([2,2])
    t6 = torch.t(t5)
    print(t5)
    print(t6)

结果如下

![20211220152855](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211220152855.png)

##### tensor.transpose()

    t3 = torch.Tensor(np.arange(24).reshape(2, 3, 4))
    print(t3.size())  # torch.Size([2, 3, 4])
    # 把第二个和第一个维度数据交换
    t4 = t3.transpose(0, 1)
    print(t4.size())   # torch.Size([3, 2, 4])

![20211220172835](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211220172835.png)

##### tensor.permute()

    t3 = torch.Tensor(np.arange(24).reshape(2, 3, 4))
    print("t3",t3.size())  # torch.Size([2, 3, 4])
    # 把第一个维度放到最后
    t7 = t3.permute(1, 2, 0)
    print("t7",t7.size())  # torch.Size([3, 4, 2])

#### 3.8 取值，切片，赋值

![20211220155513](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211220155513.png)

#### 3.9 数据类型

![20211220163654](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211220163654.png)

##### 指定数据类型

torch.tensor(array,dtype)

torch.ones(array,dtype)

##### 获取数据类型

tensor.dtype

##### 修改数据类型

tensor.float()/long()/int()

## 三.tensor的其他操作

### 1.tensor之间运算

torch.add(x,y)

x.add(y)

x.add_(y)  带下划线的方法会对x的值进行就地修改

### 2.tensor和数字操作

### 3.CUDA中的tensor

支持返回True,否则为False

    print(torch.cuda.is_available())

![20211220224649](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211220224649.png)





