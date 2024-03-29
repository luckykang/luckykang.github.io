---
layout: post
title: OpenVINO系列15丨Model Optimizer介绍与使用(一)
tag: OpenVINO
---


### 1.获取模型的方法有几种
1.自己训练一个模型

2.在线网站下载

[https://download.01.org/opencv/2020/openvinotoolkit/2020.4/open_model_zoo/](https://download.01.org/opencv/2020/openvinotoolkit/2020.4/open_model_zoo/)


如下图是image-retrieval-0001模型的IR文件，可以直接下载

![0911141740](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0911141740.png)

3.openvino的 model-downloader 下载

![0911144425](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0911144425.png)

### 2.为什么需要使用Model Optimizer

一方面，现在有很多深度学习框架在产业界被广泛的使用，像caffe、tensorflow、mxnet,而每几个月就会有新的框架。我们想在所有的框架中做推理。

另一方面Intel有丰富的计算类型比如CPU、GPU、VPU、FPGA等供大家做选择，要能够有软件套件对应上述任何不同的计算类型是非常复杂的。一是因为所有深度学习框架的表示法都是不同的，例如tf和caffe的表示法；二是每个计算类型有不用的系统架构，不用的指令集和程序编写模式，如果我们自己去学习编写VPU或者FPGA的程序，会花费很多的时间，也需要学习很多的技能，所以Intel提供了单一的共享软件开发接口(common-API),方便开发推理程序并在不同的计算类型上运行，基本上帮助做到硬件的抽象化。这工作分为两部分：阶段一是转换所有的模型成一个统一的表示法称作IR，阶段二是使用这些IR文件来让推理能执行在不同的计算类型上，Model Optimizer就是执行第一阶段的任务，接收不同表示法的模型，然后 1.转换 2.优化 3.转换权值和偏移量。

### 3.Model Optimizer工作原理

Model Optimizer将模型加载到内存中，读取模型并构建模型的内部表示，对其进行优化，然后生成中间表示（IR），Model Optimizer是一个离线工具，运行一次就可以生成IR文件(.xml和.bin),模型优化器的使用和硬件没有关系。

.xml文件，它描述整个模型拓扑，每个阶层，相连性和参数值。

.bin文件，它包含每层已经训练好的权值和偏移值。


### 4.Model Optimizer有哪些功能

1.可以对深度学习框架caffe、mxnet、onnx、tf、kaldi的模型进行转换

![20210421143128](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210421143128.png)

2.优化网络图层，减少推理时间。预训练模型包含一些对于训练很重要的图层，但在执行推理阶段无用，有可能增加推理时间，通过优化可以精简图层。

3.将模型网络映射到支持的库、类和或者层

```
mo.py

    --scale_values [R,G,B]
```

深度网络往往不会直接使用图像像素，而是先对其进行校转化和缩放，通常会将每个训练的图像减去被用于训练图像的平均值。scale_values的值可以帮助把每个通道的数值除以不同的数值，通道代表的信息和顺序取决于原始模型受训练的方式，每个通道减去平均值的操作也很容易。

    --reverse_input_channel

对输入的顺序进行反转，比如将RGB转换为BGR，必须要注意的是模型的输入通道的顺序需要和推理图片的通道顺序保持一致

4.优化神经网络

```
mo.py

    --disable_fusing
```

如果你对模型优化器不感兴趣，只需要运行模型优化器的禁用融合功能，就可以不使用模型优化器对模型所做的优化。

5.更改格式

模型优化器可以将数据和权重的格式从FP32更改为FP16，从浮点到整数的转换是通过模型优化器的外部专用校准流程来完成的。

6.剪切部分网络

mo.py

    --input pool1

模型优化器可以剪切网络的相应部分，比如运行`--input pool1`, 剪切pool1之前的所有层

### 5.了解模型优化器的输入参数

1.下载mobilenetv2-7.onnx分类

```
wget https://github.com/onnx/models/raw/master/vision/classification/mobilenet/model/mobilenetv2-7.onnx
```

![0915113830](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0915113830.png)

2.模型转换

添加均值 `--mean_values`

缩放值 `--scale_values`

RGB反转为BGR `--reverse_input_channels`

存放路径 `--output_dir` 

转换后名称 `--model_name`

```
mo.py  --input_model /home/kang/Documents/mobilenetv2-7.onnx      --mean_values=data[123.675,116.28,103.53] --scale_values=data[58.624,57.12,57.375]  --reverse_input_channels -o ~/Documents/
```

![0915113928](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0915113928.png)

3.运行推理

```
python3 classification_sample.py  -m  /home/kang/Documents/mobilenetv2-7.xml -i /opt/intel/openvino/deployment_tools/demo/car.png
```

![0915114328](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0915114328.png)

<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)