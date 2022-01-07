---
layout: post
title: 计算机视觉与TensorFlow系列02丨Tensorflow和Keras介绍与Tensorboard使用
tag: 计算机视觉与TensorFlow
---

## 一.TensorFlow介绍

### 1.TensorFlow的特点

- 语言多样

TF提供丰富的深度学习相关的API，支持python和C、C++接口。

- 使用分发策略进行分发训练
  
对于大型ML训练任务，分发策略API使得在不更改模型定义的情况下，可以轻松的在不同的硬件配置上分发和训练模型。支持一系列硬件加速器，如CPU、GPU和TPU。

- Tensorboard可视化

Tensorboard是TensorFlow的一组Web应用，用来监控TensorFlow运行过程

- 在任何平台上的生产中进行强大的模型部署

训练完成并保存了模型，就可以直接在应用程序中执行，或者使用部署库为其提供服务：

> TensorFlow服务: 允许模型通过HTTP/REST或GRPC/协议缓冲区提供服务的TensorFlow库构建。

> TensorFlow Lite: TensorFlow针对移动端和嵌入式设备的轻量级解决方案，提供了在Android、ios和嵌入式系统上部署模型的能力。

> Tensorflow.js: 支持在JavaScript环境中部署模型，例如在web浏览器或服务端通过Node.js部署模型。还支持在JavaScript中定义模型，并使用类似于Kera的API直接在web浏览器中进行训练。

### 2.TensorFlow工作流程

![20220105155613](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220105155613.png)

#### 1.使用tf.data加载数据

使用tf.data实例化读取训练数据和测试数据。

#### 2.模型的建立与调试

使用动态图模型Eager Execution和著名的神经网络高层API框架Keras，结合可视化工具TensorBoard,简易、快速地建立和调试模型；

#### 3.模型的训练

支持CPU、单GPU、单机多卡GPU、多机集群、TPU训练模型，充分利用海量数据和计算资源进行高效训练。

#### 4.预训练模型调用

通过TensorFlow Hub,可以方便地调用预训练完毕的已有成熟模型。

#### 5.模型的部署

通过TensorFlow Serving、TensorFlow Lite、TensorFlow.js等组件，可以将TensorFlow模型部署到服务器、移动端、嵌入式端等多种使用场景。

## 二.张量及操作

### 1.张量介绍

张量是一个多维数组。与Numpy ndarray对象类似，`tf.Tensor`对象也具有数据类型和形状。此外，`tf.Tensor`可以保留在GPU中。TensorFlow提供了丰富的操作库(tf.add,tf.matmul,tf.linalg.inv等)，他们使用和生成`tf.Tensor`。在进行张量操作之前先导入相应的工具包。

```python
import tensorflow as tf
import numpy as np
```

### 2.张量操作

```python

import tensorflow as tf
# 忽略
import os

os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'

# 创建int32类型的0维张量，即标量
tensor_0 = tf.constant(3)
print(tensor_0)
# 创建float32类型的1维张量
tensor_1 = tf.constant([2.0, 3.0, 4.0])
print(tensor_1)
# 创建float16类型的二维张量
tensor_2 = tf.constant([
    [2, 3],
    [4, 6],
    [5, 7]
], dtype=tf.float16)
print(tensor_2)

```
结果
```python
tf.Tensor(3, shape=(), dtype=int32)
tf.Tensor([2. 3. 4.], shape=(3,), dtype=float32)
tf.Tensor(
[[2. 3.]
 [4. 6.]
 [5. 7.]], shape=(3, 2), dtype=float16)
```

![20220105174158](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220105174158.png)

### 3.张量转换成numpy

#### 方法1：`np.array`

np.array(tensor_2)

#### 方法2：`Tensor.numpy()`

tensor_2.numpy()

演示：

![20220105175644](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220105175644.png)

### 4.张量的常用函数

```python
import tensorflow as tf
# 忽略
import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'

a = tf.constant([
    [1, 2],
    [2, 3],
])
b = tf.constant([
    [3, 4],
    [4, 5],
])
# 计算张量的和
print(tf.add(a, b), "\n")
# 计算张量的元素乘法
print(tf.multiply(a, b), "\n")
# 计算乘法
print(tf.matmul(a, b), "\n")
```
结果
```python
tf.Tensor(
[[4 6]
 [6 8]], shape=(2, 2), dtype=int32) 

tf.Tensor(
[[ 3  8]
 [ 8 15]], shape=(2, 2), dtype=int32) 

tf.Tensor(
[[11 14]
 [18 23]], shape=(2, 2), dtype=int32)
```

### 5.聚合运算

```
tf.reduce_sum()
tf.reduce_mean()
tf.reduce_max()
tf.reduce_min()
tf.argmax()
tf.argmin()
```

### 6.变量

变量是一种特殊的张量，形状是不可变的，但可以更改其中的参数。

```python
var = tf.Variable([[1, 3], [4, 6]])
# 形状
print("shape", var.shape)
# 类型
print("dtype", var.dtype)
# 转换为ndarray
print("ndarray", var.numpy())
# 改变参数
print("assign", var.assign([[2, 3], [6, 7]]))
```

## 三.tf.keras介绍

### 1.常用模块

![20220106153215](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220106153215.png)

### 2.常用方法

- 1.导入tf.keras

```python
import tensorflow as tf
from tensorflow import keras
```
- 2.数据输入

对于小的数据集，可以直接使用numpy格式的数据进行训练、评估模型，对于大型数据集或者要进行跨设备训练时可使用`tf.data.datasets`来进行数据输入。

- 3.模型构建

简单模型使用Sequential进行构建

复杂模型使用函数式编程来构建

自定义layers

- 4.训练与评估

![20220106161920](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220106161920.png)

- 5.回调函数(callbacks)

![20220106162304](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220106162304.png)

- 6.模型的保存和恢复

> 只保存参数

```python
# 只保存模型的权重
model.save_weights('./model')
# 加载模型的权重
model.load_weights('model')
```
> 保存整个模型
```python
# 保存模型架构与权重在h5文件中
model.save('model.h5')
# 加载模型：包括架构和对应的权重
model = keras.models.load_model('model.h5')
```

## 四.Tensorboard介绍与使用

TensorFlow有一个亮点就是，我们能看到自己写的程序的可视化效果，这个功能就是Tensorboard。它是一个可视化工具。通过它，我们可以更方便地对 TensorFlow 程序的理解、调试与优化。

**那么如何实现程序可视化的?**

> 第一步：数据序列化-events文件

TensorBoard 通过读取 TensorFlow 的事件文件来运行，需要将数据生成一个序列化的 ***Summary protobuf*** 对象。

    # 返回filewriter,写入事件文件到指定目录(最好用绝对路径)，以提供给tensorboard使用
    tf.summary.FileWriter('./tmp/summary/', graph=sess.graph)

这将在指定目录中生成一个 **event** 文件，其名称格式如下：

    events.out.tfevents.{timestamp}.{hostname}

> 第二步：启动Tensorboard

    tensorboard  --logdir="./tmp/tensorflow/summary/"

在浏览器中打开 TensorBoard 的图页面 `127.0.0.1:6006` ，会看到与以下图形类似的图,在GRAPHS模块我们可以看到以下图结构

代码演示：

    import tensorflow as tf
    import os
    os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'

    def graph_demo():
        # 创建图
        new_g = tf.Graph()
        with new_g.as_default():
            new_a = tf.constant(13.0)
            new_b = tf.constant(14.0)
            new_c = tf.add(new_a, new_b)
        # 开启会话,参数要指向创建的图，如果不指定则指定默认图
        with tf.compat.v1.Session(graph=new_g) as sess:
            sum_t = sess.run(new_c)
            print("在sess当中的sum_t:\n", sum_t)
            # 写入到events文件中
            filewriter = tf.compat.v1.summary.FileWriter("./tmp/summary/", graph=sess.graph)
    if __name__ == '__main__':
        graph_demo()

生成event文件：

![20211126171600](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211126171600.png)

在当前项目路径的的虚拟环境中，输入下面命令

![20211126172211](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211126172211.png)

即会启动一个6006端口的本地服务，我们可以查看程序的详情

![20211126172152](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211126172152.png)

