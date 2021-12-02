**---
layout: post
title: openvino_tensorflow介绍与使用
tag: OpenVINO
---**

## 一.介绍

### 1.为什么要使用？

`openvino_tensorflow`是为想尝试把openvino用于推理应用中的tensorflow开发者们设计的。原理是通过OpenVINO的内联优化，只需少量代码的改动就可以
显著提高其推理性能。该仓库展示了对TensorFlow的良好兼容性，可以多众多的AI模型都有加速效果。

### 2.在哪些计算单元有加速效果？

- 英特尔 CPU
- 英特尔集成 GPU
- 英特尔 Movidius视觉处理单元，通常称为 VPU
- 采用 8 个英特尔 Movidius VPU 的英特尔视觉加速器设计，通常称为 VAD-M 或 HDDL

## 二.安装

### 1.支持的环境

- Ubuntu 18.04, 20.04 or MacOS 11.2.3
- Python 3.6, 3.7, 3.8 or 3.9
- TensorFlow v2.5.1

### 2.界面选项与配置

单击下面的链接，查看并根据实际环境选择安装选项菜单。

[https://openvinotoolkit.github.io/openvino_tensorflow/](https://openvinotoolkit.github.io/openvino_tensorflow/)

下图是我在安装选择菜单。如果预装了tf,建议在虚拟环境中使用。

![20211201222854](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211201222854.png)

然后运行显示的安装命令。安装包附带 OpenVINO 2021.4.1 的预构建库，所以我们无需再单独安装 OpenVINO。

    pip3 install pip==21.0.1
    pip3 install tensorflow==2.5.1
    pip3 install -U openvino-tensorflow

![20211201231731](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211201231731.png)
![20211201231826](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211201231826.png)

使用pip list查看安装情况

![20211201232010](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211201232010.png)

检验openvino_tensorflow 是否安装正确

    python3 -c "import tensorflow as tf; print('TensorFlow version: ',tf.__version__);\
                import openvino_tensorflow; print(openvino_tensorflow.__version__)"

当我们看到下图的返回信息，说明安装OK的

![20211202001733](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202001733.png)

## 三.Demo的演示体验

### 1.clone仓库

![20211202003330](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202003330.png)

    $ git clone https://github.com/openvinotoolkit/openvino_tensorflow.git
    $ cd openvino_tensorflow
    $ git submodule init
    $ git submodule update --recursive

### 2.对象检测的python实现

安装环境依赖

![20211202003500](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202003500.png)

由于模型文件比较大没打包在存储库中，我们需要执行一下脚本`convert_yolov3.sh`。这个脚本实现的功能是将 DarkNet 模型转换为 TensorFlow模型，并将标签和权重下载到“openvino_tensorflow 的存储库”中的data路径下：

![20211202004028](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202004028.png)

    $ chmod +x convert_yolov3.sh
    $ ./convert_yolov3.sh

查看data目录，有了我们需要的coco.names和pb文件

![20211202004309](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202004309.png)

使用下面命令查看所有的参数设置。

    python3 examples/object_detection_sample.py --help

![20211202010326](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202010326.png)

最后一项`--disable_ovtf`参数的意思是不使用openvino进行加速，将使用默认的tf进行推理。这里我们首先使用默认的tf进行推理，看看推理用时怎么样。我这里用了自己的照片，所以指定了`--input`参数。不加则使用默认提供的。

    python3 examples/object_detection_sample.py --disable_ovtf -input=/home/kang/kang.jpeg


![20211202010502](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202010502.png)

    python3 examples/object_detection_sample.py --input=/home/kang/kang.jpeg

得到推理结果，推理耗时733.25ms,推理设备为CPU。

![20211202005900](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202005900.png)

### 3.分类检测的C++实现





## 四.oneDNN介绍与使用

我们可以通过设置环境变量 `TF_ENABLE_ONEDNN_OPTS=1`来启用`oneDNN Deep Neural Network Library (oneDNN)`,来获得最佳效果。

### 1.什么是oneDNN

oneDNN全称oneAPI Deep Neural Network Library，是一个开源的跨平台性能库，包含深度学习应用的基本构件。 oneDNN是oneAPI的一部分，针对Intel处理器和显卡进行了优化。也对以下平台有实验性的支持：Arm* 64-bit Architecture (AArch64), NVIDIA GPU, OpenPOWER Power ISA (PPC64), IBMz (s390x), and RISC-V。

### 2.下载

下载link:[https://www.intel.com/content/www/us/en/developer/tools/oneapi/onednn.html#gs.i53t68](https://www.intel.com/content/www/us/en/developer/tools/oneapi/onednn.html#gs.i53t68)

点击link,在下图显示页面点击下载

![20211202000305](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202000305.png)

选择系统与下载方式：

![20211202000810](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202000810.png)

### 3.安装

    sudo bash l_BaseKit_p_2021.4.0.3422_offline.sh

![20211202012052](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202012052.png)

![20211202012228](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202012228.png)


