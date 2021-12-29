---
layout: post
title: NLP与Pytorch08丨Pytorch的数据加载
tag: NLP
---

## 一. 模型中使用数据加载器的目的

在前面的线性回归模型中，我们使用的数据很少，所以直接把全部数据放到模型中去使用。

但是在深度学习中，数据量通常是都非常多，非常大的，如此大量的数据，不可能一次性的在模型中进行向前的计算和反向传播，经常我们**会对整个数据进行随机的打乱顺序，把数据处理成一个个的batch，同时还会对数据进行预处理**。

## 二. 数据集类

### 2.1 Dataset基类介绍

在torch中提供了数据集的基类`torch.utils.data.Dataset`，继承这个基类，我们能够非常快速的实现对数据的加载。

`torch.utils.data.Dataset`的源码如下：

```python
class Dataset(object):
    """An abstract class representing a Dataset.

    All other datasets should subclass it. All subclasses should override
    ``__len__``, that provides the size of the dataset, and ``__getitem__``,
    supporting integer indexing in range from 0 to len(self) exclusive.
    """

    def __getitem__(self, index):
        raise NotImplementedError

    def __len__(self):
        raise NotImplementedError

    def __add__(self, other):
        return ConcatDataset([self, other])
```

可知：我们需要在自定义的数据集类中继承Dataset类，同时还需要实现两个方法：

1. `__len__`方法，能够实现通过全局的`len()`方法获取其中的元素个数
2. `__getitem__`方法，能够通过传入索引的方式获取数据，例如通过`dataset[i]`获取其中的第`i`条数据

### 2.2 数据加载案例

下面通过一个例子来看看如何使用Dataset来加载数据

数据来源：`http://archive.ics.uci.edu/ml/datasets/SMS+Spam+Collection`

数据介绍：SMS Spam Collection是用于骚扰短信识别的经典数据集，完全来自真实短信内容，包括4831条正常短信和747条骚扰短信。正常短信和骚扰短信保存在一个文本文件中。 每行完整记录一条短信内容，每行开头通过ham和spam标识正常短信和骚扰短信。

实现如下：

```python
from torch.utils.data import Dataset
import pandas as pd

# 数据集路径
data_path = r"SMSSpamCollection"

class CifarDataset(Dataset):
    def __init__(self):
        lines = open(data_path, "r", encoding='utf-8')
        # 对数据进行处理，前4个为label，后面的为短信内容
        lines = [[i[:4].strip(), i[4:].strip()] for i in lines]
        # 转化为dataFrame
        self.df = pd.DataFrame(lines, columns=["label", "sms"])

    def __getitem__(self, index):
        single_item = self.df.iloc[index, :]
        return single_item.values[0], single_item.values[1]

    def __len__(self):
        return self.df.shape[0]


if __name__ == '__main__':
    d = CifarDataset()
    for i in range(len(d)):
        print(i, d[i])
```

输出如下：

![20211228181042](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211228181042.png)

## 三. 迭代数据集

使用上述的方法能够进行数据的读取，但是其中还有很多内容没有实现：

- 批处理数据（Batching the data）
- 打乱数据（Shuffling the data）
- 使用多线程 `multiprocessing` 并行加载数据。

在pytorch中`torch.utils.data.DataLoader`提供了上述的所用方法

`DataLoader`的使用方法示例：

```python
from torch.utils.data import DataLoader

dataset = CifarDataset()
# batch_size指定一组有10个数，shuffle=True打乱顺序，num_workers指定线程数
data_loader = DataLoader(dataset=dataset,batch_size=10,shuffle=True,num_workers=2)

#遍历，获取其中的每个batch的结果
for index, (label, context) in enumerate(data_loader):
    print(index,label,context)
    print("*"*100)
```

其中参数含义：

1. dataset：提前定义的dataset的实例
2. batch_size:传入数据的batch的大小，常用128,256等等
3. shuffle：bool类型，表示是否在每次获取数据的时候提前打乱数据
4. `num_workers`:加载数据的线程数



代码如下：

```
from torch.utils.data import Dataset,DataLoader
import pandas as pd

data_path = r"SMSSpamCollection"

class CifarDataset(Dataset):
    def __init__(self):
        lines = open(data_path, "r", encoding='utf-8')
        # 对数据进行处理，前4个为label，后面的为短信内容
        lines = [[i[:4].strip(), i[4:].strip()] for i in lines]
        # 转化为dataFrame
        self.df = pd.DataFrame(lines, columns=["label", "sms"])

    def __getitem__(self, index):
        single_item = self.df.iloc[index, :]
        return single_item.values[0], single_item.values[1]

    def __len__(self):
        return self.df.shape[0]

d = CifarDataset()
data_loader = DataLoader(dataset=d,batch_size=2,shuffle=True)

if __name__ == '__main__':
    for i in data_loader:
        print(i)
        break
```


使用break可以看到每次的结果不一致，说明shuffle=True是生效的。结果如下：

![20211228182416](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211228182416.png)

注意：

1. `len(dataset) = 数据集的样本数`
2. `len(dataloader) = math.ceil(样本数/batch_size) 即向上取整` 

![20211228183641](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211228183641.png)

**drop_last=True**可以删除最后一个batch

![20211228183958](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211228183958.png)



## 四.pytorch自带的数据集

pytorch中自带的数据集由两个上层api提供，分别是`torchvision`和`torchtext`

其中：

1. `torchvision`提供了对图片数据处理相关的api和数据
   - 数据位置：`torchvision.datasets`，例如：`torchvision.datasets.MNIST`(手写数字图片数据)
2. `torchtext`提供了对文本数据处理相关的API和数据
   - 数据位置：`torchtext.datasets`,例如：`torchtext.datasets.IMDB（电影`评论文本数据）

下面我们以Mnist手写数字为例，来看看pytorch如何加载其中自带的数据集

使用方法和之前一样：

1. 准备好Dataset实例
2. 把dataset交给dataloder 打乱顺序，组成batch

### 4.1 torchversion.datasets

`torchversoin.datasets`中的数据集类（比如`torchvision.datasets.MNIST`）,都是继承自`Dataset`

意味着：直接对`torchvision.datasets.MNIST`进行实例化就可以得到`Dataset`的实例

但是MNIST API中的参数需要注意一下：

` torchvision.datasets.MNIST(root='/files/', train=True, download=True, transform=)`

1. `root`参数表示数据存放的位置
2. `train：`bool类型，表示是使用训练集的数据还是测试集的数据
3. `download：`bool类型，表示是否需要下载数据到root目录
4. `transform:`实现的对图片的处理函数



### 4.2 MNIST数据集的介绍

数据集的原始地址：`http://yann.lecun.com/exdb/mnist/`

MNIST是由`Yann LeCun`等人提供的免费的图像识别的数据集，其中包括60000个训练样本和10000个测试样本，其中图拍了的尺寸已经进行的标准化的处理，都是黑白的图像，大小为`28X28`


执行代码，下载数据，观察数据类型：

```python
import torchvision

dataset = torchvision.datasets.MNIST(root="./data",train=True,download=True,transform=None)

print(dataset[0])
```

下载的数据如下：

![20211229155134](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211229155134.png)

其中数据集返回了两条数据，可以猜测为图片的数据和目标值

![20211229155225](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211229155225.png)

返回值的第0个为Image类型，可以调用show() 方法打开，发现为手写数字5

```python
import torchvision

dataset = torchvision.datasets.MNIST(root="./data",train=True,download=True,transform=None)

print(dataset[0])

img = dataset[0][0]
img.show() #打开图片
```

图片如下：

![20211229154247](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211229154247.png)

由上可知：返回值为`(图片，目标值)`,这个结果也可以通过观察源码得到。


