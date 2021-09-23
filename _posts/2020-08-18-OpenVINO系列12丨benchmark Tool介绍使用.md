---
layout: post
title: OpenVINO系列12丨benchmark Tool介绍使用
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

同步模式中,主要指标是延迟。该程序的执行通过是创建推断请求并执行Infer方法实现的。在程序执行期间，应用程序收集每个执行的推断请求的延迟。主要收集两类指标：使用Infer方法执行的每个推断请求的延迟和所有执行的持续时间。

异步模式中，主要指标是每秒的帧吞吐量(FPS)。如果以异步模式运行该应用程序，它将创建与-nireq命令行参数中指定数量一样多的推断请求，并执行该StartAsync方法。推断请求是异步执行的。回调用于等待先前的执行完成。该应用程序会测量所有推断请求的执行情况，并基于批处理大小和总执行持续时间来报告吞吐量指标。

### 四、同步模式下的基准测试--C++版本

以squeezenet1.1模型为例，我们使用C++版本的benchmark在同步模式下的进行基准测试，运行设备为CPU

#### 1.下载squeezenet1.1，并指定下载路径

![0909094737](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0909094737.png)

#### 2.用 Model Optimizer 转化成IR文件

![0909101539](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0909101539.png)


#### 3.在CPU设备上同步模式运行Benchmark

运行之前我们要先编译Benchmark。运行的时候要注意，参数 `-api` <sync/async>可选启用同步/异步API，这里我们使用同步 `-api sync`

### 运行步骤分为11步:
1.解析和验证输入参数

2.加载推理引擎

3.设置硬件的配置，我们用的是CPU

4.加载中间网络的文件

5.调整网络大小以匹配图像大小和给定批次

6.配置模型的输入

7.加载模型到CPU

8.设置最佳的运行参数

9.创建推断请求并用图像填充输入blob

10.测量性能（同步开始推理，持续时间为60000 ms）

11.显示统计报告

统计报告显示了在CPU上的性能参数，包括计数为21649次迭代、持续时间为60001.82 ms、延迟为2.56 ms、吞吐量为389.86 FPS

![0909103304](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0909103304.png)

### 五、异步模式下的基准测试--python版本

这里我们使用python版本的benchmark测试squeezenet1.1模型在异步模式下CPU设备的基准性能，参数 `-api` <sync/async>可选启用同步/异步API，默认是异步执行。

#### 1.安装依赖环境

![0909112634](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0909112634.png)

#### 2.CPU设备上异步模式运行Benchmark

下图可以看到，推理执过程中有4个推理请求使用了4个CPU流

![0909130932](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0909130932.png)


### 结论

benchmark tool的c++和python版本的性能差异基本可以忽略，通过两种方式的运行可以看到，同步执行的时候吞吐量是389.86FPS,异步执行时候的吞吐量是403.72 FPS，所以可以使用异步执行的方式来提高运行性能。

这是为什么呢？

是因为我们处理的视频或者图像，在同步模式下每个阶段要等待流水线完成，这样就会降低性能。在异步模式下是通过发送推理请求，而不是等待完成，然后继续准备下一帧或实施其他任务，当推理请求没有阻止执行时，可以准备相关帧，并且进行推理，这样可以带来吞吐量的巨大提升。

<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)
