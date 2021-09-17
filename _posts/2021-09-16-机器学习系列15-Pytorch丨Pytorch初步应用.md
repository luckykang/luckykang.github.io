---
layout: post
title: 机器学习系列16-Pytorch丨Pytorch初步应用
tag: 机器学习
---

### 一、使用Pytorch构建一个神经网络

#### 1.构建神经网络的典型流程

> 关于torch.nn

- 使用Pytorch来构建神经网络，主要的工具都在torch.nn包中
- nn依赖于autograd来定义模型，并对其自动求导

> 构建神经网络的典型流程

- 定义一个拥有可学习参数的神经网络
- 遍历训练数据集
- 处理输入数据使其流经神经网络
- 计算损失值
- 将网络参数的梯度进行反向传播
- 以一定的规则更新网络的权重

> 定义一个pytorch实现的神经网络

import torch
import torch.nn as nn
import torch.nn.functional as F 

# 定义网络类

    class Net(nn.Module):
        def __init__(self):
            super(Net,self).__init__()
            # 定义第一层卷积层，输入维度=1，输出维度=6，卷积核大小3*3
            self.conv1 = nn.Conv2d(1,6,3)
            # 定义第二层卷积层，输入维度=6，输出维度=16，卷据核大小3*3
            self.conv2 = nn.Conv2d(6,16,3)
            # 定义三层全连接神经网络
            self.fc1 = nn.Linear(16*6*6,120)
            self.fc2 = nn.Linear(120,84)
            # 这里的10代表十个分类器
            self.fc3 = nn.Linear(84,10)

        def forward(self,x):
            # 注意：任意卷积层后面要加激活层，池化层
            x = F.max_pool2d(F.relu(self.conv1(x)),(2,2))
            x = F.max_pool2d(F.relu(self.conv2(x)),2)
            # 经过卷积层的处理后，张量要进入全连接层，进入前需要调整张量的形状
            x = x.view(-1,self.num_flat_features(x))
            x = F.relu(self.fc1(x))
            x = F.relu(self.fc2(x))
            x = self.fc3(x)
            return x

        def num_flat_features(self,x):
            size = x.size()[1:]
            num_features = 1
            for s in size:
                num_features *= s
            return num_features
        
    net = Net()
    print(net)

输出结果

![20210917150808](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210917150808.png)

**注意**

模型中的所有的可训练参数，可以通过net.parameters()来获得

    params = list(net.parameters())
    print(len(params))
    # 打印第一个参数
    print(params[0].size())

输出结果

    10
    torch.Size([6, 1, 3, 3])

假设图像的输入尺寸为32*32

    input = torch.randn(1,1,32,32)
    out = net(input)
    print(out)

输出结果

    tensor([[ 0.0549,  0.1465,  0.0975,  0.0032, -0.0572,  0.0673, -0.0140, -0.0227,
            -0.0324, -0.1341]], grad_fn=<AddmmBackward>)

有个输出张量以后，就可以执行梯度归零和反向传播了













### 二、使用Pytorch构建一个分类器

