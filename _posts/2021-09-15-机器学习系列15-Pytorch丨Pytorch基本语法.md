---
layout: post
title: 机器学习系列15-Pytorch丨Pytorch基本语法
tag: 机器学习
---

### 一、什么是Pytorch

Pytorch是是一个基于Numpy的科学计算包，向他的使用者提供两大功能：

- 作为Numpy的替代者，向用户提供使用GPU强大功能的能力。

- 作为一款深度学习的平台，向用户提供最大的灵活性和速度。

### 二、Pytorch的基本元素操作

#### 1.Tensors张量

张量的概念类似于Numpy中的ndarray数据结构，最大的区别在于Tensor可以利用GPU的加速功能。

我们使用Pytorch的时候，常规步骤是先将torch引用进来，如下所示：

        from __future__ import print_function
        import torch 

#### 2.创建矩阵操作

> 创建一个没有初始化的矩阵

    x = torch.empty(5,3)


![20210915131544](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915131544.png)

> 创建有初始化的矩阵

    x = torch.rand(5,3)


![20210915112812](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915112812.png)

当声明一个未初始化的矩阵时，它本身不包含任何确切的值，创建的时候，分配给矩阵的内存中有什么数值就赋值给了这个矩阵，本质上是毫无意义的数据。

> 创建一个全零矩阵

    x = torch.zeros(5,3,dtype=torch.long)

![20210915132733](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915132733.png)

> 直接通过数据创建张量

    x = torch.tensor([2.5,3.5])   

![20210915133118](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915133118.png)

> 通过已有的一个张量创建相同尺寸的新张量

    # 利用news_mathods方法得到一个张量
    x = x.new_ones(5,3,dtype=torch.double)

![20210915134144](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915134144.png)

    # 利用randn_like方法得到相同张量尺寸的一个新张量，并且采用随机初始化来对其赋值
    y = torch.randn_like(x,dtype=torch.float)

![20210915135840](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915135840.png)

    # 采用.size()方法得到张量的形状
    print(x.size())
    print(y.size())

![20210915140259](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915140259.png)

**注意** 

torch.Size函数本质上返回的是一个tuple,因此它支持一切元组的操作。

### 三、基本运算操作

#### 1.加法操作

    x = torch.rand(5,3)
    y = torch.rand(5,3)
    print(x + y)

![20210915141016](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915141016.png)

> 第二种方法 add

    print(torch.add(x,y))

> 第三种方法  result

    result = torch.empty(5,3)
    torch.add(x,y,out=result)
    print(result)

> 第四种方法 in-place（原地置换）

y.add_(x)
print(y)

#### 2.改变张量的形状：torch.view()

    # 改变张量的形状：torch.view()
    x = torch.randn(4,4)
    # tensor.view()操作需要保证数据元素的总数量不变
    y = x.view(16)
    # -1代表自动匹配个数,8为8列
    z = x.view(-1,8)
    print(x)
    print(y)
    print(z)
    print(x.size(),y.size(),z.size())  # torch.Size([4, 4]) torch.Size([16]) torch.Size([8, 2])

#### 3.item()

如果张量中只有一个元素，可以用.item()将值取出，作为一个python number

    x = torch.randn(1)
    print(x)
    print(x.item())

![20210915160529](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915160529.png)

#### 4.Torch Tensor和Numpy array的相互转换

> 将torch tensor转换为Numpy array

Torch Tensor和Numpy array共享底层的内存空间，因此改变其中一个值，另一个也会随之被改变。

    a = torch.ones(6) # tensor([1., 1., 1., 1., 1., 1.])
    # 将torch tensor转换为Numpy array
    b = a.numpy() # [1. 1. 1. 1. 1. 1.]
    # 对其中一个进行加法操作，另一个也随之被改变
    a.add_(1)
    print(a) # tensor([2., 2., 2., 2., 2., 2.])
    print(b) # [2. 2. 2. 2. 2. 2.]

> 将Numpy array转换为Torch Tensor

    a = np.ones(5)
    b = torch.from_numpy(a)
    # a的值+2,并把结果赋值给a
    np.add(a,2,out=a)
    print(a)
    print(b)

![20210915170546](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915170546.png)

**注意** 所有在CPU上的Tensors,除了CharTensor,都可以转换为Numpy array并可以反向转换。


#### 5.to()方法

任意的Tensors可以用.to()方法来将其移动到任意设备上

![20210915181256](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915181256.png)

执行结果，可以看到第一行的结果是GPU执行的，第二行的结果是CPU上执行的。

![20210915180614](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210915180614.png)