---
layout: post
title: OpenVINO的benchmark Tool介绍使用
tag: OpenVINO
---

### 一、什么是benchmark Tool

Benchmark Tool用来评估支持的设备上的深度学习推理性能，对于所有开箱即用的设备通常都能产生最佳性能。

它可以针对两种推理模式测量性能：

- 同步(Synchronous) --> 面向延迟
- 异步(Asynchronous) --> 面向吞吐量
### 二、benchmark是怎么运行的

启动时，应用程序将读取命令行参数，并将网络和图像/二进制文件加载到Inference Engine插件中，该插件的选择取决于指定的设备。推理请求的数量和执行方法取决于-api命令行参数定义的模式。

### 三、同步模式和异步模式

同步模式中,主要指标是延迟。该程序的执行通过是创建推断请求并执行Infer方法实现的。在程序执行期间，应用程序收集每个执行的推断请求的延迟。主要收集两类指标：

- 使用Infer方法执行的每个推断请求的延迟
- 所有执行的持续时间

异步模式中，主要指标是每秒的帧吞吐量(FPS)。如果以异步模式运行该应用程序，它将创建与-nireq命令行参数中指定数量一样多的推断请求，并执行该StartAsync方法。推断请求是异步执行的。回调用于等待先前的执行完成。该应用程序会测量所有推断请求的执行情况，并基于批处理大小和总执行持续时间来报告吞吐量指标。

以下参数定义了许多执行步骤：

- -niter  命令行参数定义的迭代次数
- -t  命令行参数指定的持续时间
- 两者（继续执行直到满足两个条件）
- 预定持续时间如果 -niter 和 -t 未指定，预定的持续时间值取决于设备。

### 四、 googlenet-v1模型在同步模式下的基准测试，运行设备为CPU（C++演示）

#### 1.下载googlenet-v1，并指定下载路径

![0818155803](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818155803.png)

#### 2.用 Model Optimizer 转化成IR文件

![0818160514](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818160514.png)


#### 3.CPU设备上同步模式运行Benchmark Tool

参数：`-api` <sync/async>可选,启用同步/异步API,默认值为“异步”,这里我们使用同步模式运行，获得它在本地设备上的性能参数，包括计数、持续时间、延迟、吞吐量

![0818164155](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818164155.png)


### 五、 googlenet-v1模型在异步模式下的基准测试，运行设备为CPU（python演示）

#### 1.下载googlenet-v1，并指定下载路径

![0818155803](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818155803.png)

#### 2.用 Model Optimizer 转化成IR文件

![0818160514](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818160514.png)


#### 3.CPU设备上异步模式运行Benchmark Tool

参数：`-api` <sync/async>可选,启用同步/异步API,默认值为“异步”,这里我们使用异步模式运行，获得它在本地设备上的性能参数，包括计数、持续时间、延迟、吞吐量

![0818165700](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818165700.png)
![0818165819](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0818165819.png)



### 结论
通过同步异步执行的方式可以得出，同步执行的时候吞吐量是85.00 FPS,异步执行时候的吞吐量是100.43 FPS，所以可以使用异步执行的方式来提高运行性能。

<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)
