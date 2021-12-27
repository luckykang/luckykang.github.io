---
layout: post
title: NLP与Pytorch06丨调用Pytorch API完成线性回归
tag: NLP
---

## 一. `nn.Module`

`nn.Module` 是`torch.nn`提供的一个类，是pytorch中`自定义网络`的一个基类，在这个类中定义了很多有用的方法，让我们在继承这个类定义网络的时候非常简单。

当我们自定义网络的时候，有两个方法需要特别注意：

1. `__init__`需要调用`super`方法，继承父类的属性和方法
2. `farward`方法必须实现，用来定义我们的网络的向前计算的过程

用前面的`y = wx+b`的模型举例如下：

```python
from torch import nn
class Lr(nn.Module):
    def __init__(self):
        #继承父类init的参数
        super(Lr, self).__init__()  
        # 输入的特征数量，输出的特征数量
        self.linear = nn.Linear(1, 1) 

    def forward(self, x):
        out = self.linear(x)
        return out
```

注意：

1. `nn.Linear`为torch预定义好的线性模型，也被称为**全链接层**，传入的参数为输入的数量，输出的数量(in_features, out_features),是不算(batch_size的列数)
2. `nn.Module`定义了`__call__`方法，实现的就是调用`forward`方法，即`Lr`的实例，能够直接被传入参数调用，实际上调用的是`forward`方法并传入参数

```python
# 实例化模型
model = Lr()
# 传入数据，计算结果
predict = model(x)
```

## 二. 优化器类

优化器(`optimizer`)，可以理解为torch为我们封装的用来进行更新参数的方法，比如常见的随机梯度下降(`stochastic gradient descent,SGD`)

优化器类都是由`torch.optim`提供的，例如

1. `torch.optim.SGD(参数，学习率)`
2. `torch.optim.Adam(参数，学习率)`

注意：

1. 参数可以使用`model.parameters()`来获取，获取模型中所有`requires_grad=True`的参数
2. 优化类的使用方法
   1. 实例化
   2. 所有参数的梯度，将其值置为0
   3. 反向传播计算梯度
   4. 更新参数值

示例如下：

```python
optimizer = optim.SGD(model.parameters(), lr=1e-3) #1. 实例化
optimizer.zero_grad() #2. 梯度置为0
loss.backward() #3. 计算梯度
optimizer.step()  #4. 更新参数的值
```

## 三. 损失函数

前面的例子是一个回归问题，torch中也预测了很多损失函数

1. 均方误差:`nn.MSELoss()`,常用于回归问题
2. 交叉熵损失：`nn.CrossEntropyLoss()`，常用于分类问题

使用方法：

```python
model = Lr() #1. 实例化模型
criterion = nn.MSELoss() #2. 实例化损失函数
optimizer = optim.SGD(model.parameters(), lr=1e-3) #3. 实例化优化器类
for i in range(100):
    y_predict = model(x_true) #4. 向前计算预测值
    loss = criterion(y_true,y_predict) #5. 调用损失函数传入真实值和预测值，得到损失结果
    optimizer.zero_grad() #5. 当前循环参数梯度置为0
    loss.backward() #6. 计算梯度
    optimizer.step()  #7. 更新参数的值
```

## 四. 线性回归完整代码

```python
import torch
from torch import nn
from torch import optim
import numpy as np
from matplotlib import pyplot as plt

# 1. 定义数据
x = torch.rand([50,1])
y = x*3 + 0.8

#2 .定义模型
class Lr(nn.Module):
    def __init__(self):
        super(Lr,self).__init__()
        self.linear = nn.Linear(1,1)
		
    def forward(self, x):
        out = self.linear(x)
        return out

# 2. 实例化模型，loss，和优化器
model = Lr()
criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=1e-3)
#3. 训练模型
for i in range(30000):
    out = model(x) #3.1 获取预测值
    loss = criterion(y,out) #3.2 计算损失
    optimizer.zero_grad()  #3.3 梯度归零
    loss.backward() #3.4 计算梯度
    optimizer.step()  # 3.5 更新梯度
    if (i+1) % 20 == 0:
        print('Epoch[{}/{}], loss: {:.6f}'.format(i,30000,loss.data))
        print(list(model.parameters()))

#4. 模型评估
model.eval() #设置模型为评估模式，即预测模式
predict = model(x)
predict = predict.data.numpy()
plt.scatter(x.data.numpy(),y.data.numpy(),c="r")
plt.plot(x.data.numpy(),predict)
plt.show()
```

输出如下：

![20211227175718](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211227175718.png)

注意：

`model.eval()`表示设置模型为评估模式，即预测模式

`model.train(mode=True)` 表示设置模型为训练模式

在当前的线性回归中，上述并无区别

但是在其他的一些模型中，**训练的参数和预测的参数会不相同**，到时候就需要具体告诉程序我们是在进行训练还是预测，比如模型中存在**Dropout**，**BatchNorm**的时候

## 五.在GPU上运行代码

当模型太大，或者参数太多的情况下，为了加快训练速度，经常会使用GPU来进行训练。

此时我们的代码需要稍作调整：

1. 判断GPU是否可用`torch.cuda.is_available()`

```python
torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
>>device(type='cuda', index=0)  #使用gpu
>>device(type='cpu') #使用cpu
```

2. 把模型参数和input数据转化为cuda的支持类型

```python
model.to(device)
x_true.to(device)
```

3. 在GPU上计算结果也为cuda的数据类型，需要转化为numpy或者torch的cpu的tensor类型

```python
predict = predict.cpu().detach().numpy() 
```

   `detach()`的效果和data的相似，但是`detach()`是深拷贝，data是取值，是浅拷贝

修改之后的代码如下：

```python
import torch
from torch import nn
from torch import optim
import numpy as np
from matplotlib import pyplot as plt
import time

# 1. 定义数据
x = torch.rand([50,1])
y = x*3 + 0.8

#2 .定义模型
class Lr(nn.Module):
    def __init__(self):
        super(Lr,self).__init__()
        self.linear = nn.Linear(1,1)

    def forward(self, x):
        out = self.linear(x)
        return out

# 2. 实例化模型，loss，和优化器

device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
x,y = x.to(device),y.to(device)

model = Lr().to(device)
criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=1e-3)

#3. 训练模型
for i in range(300):
    out = model(x)
    loss = criterion(y,out)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
    if (i+1) % 20 == 0:
        print('Epoch[{}/{}], loss: {:.6f}'.format(i,30000,loss.data))

#4. 模型评估
model.eval() #
predict = model(x)
predict = predict.cpu().detach().numpy() #转化为numpy数组
plt.scatter(x.cpu().data.numpy(),y.cpu().data.numpy(),c="r")
plt.plot(x.cpu().data.numpy(),predict,)
plt.show()

```



​	