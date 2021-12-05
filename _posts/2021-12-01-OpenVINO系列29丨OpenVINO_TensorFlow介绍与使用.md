---
layout: post
title: OpenVINO系列29丨OpenVINO_TensorFlow介绍与使用
tag: OpenVINO
---

## 一.介绍

### 1.`openvino_tensorflow`插件介绍

`openvino_tensorflow`是为尝试把openvino用于推理应用中的tensorflow开发者们设计的。原理是通过OpenVINO的内联优化，只需少量代码的改动就可以
显著提高其推理性能。该仓库展示了对TensorFlow的良好兼容性，可以对众多的AI模型起到加速效果。

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

### 2.python虚拟环境配置

    sudo apt-get install build-essential libssl-dev libffi-dev python-dev
    sudo apt-get install -y python3-venv
    # 创建项目虚拟环境的目录
    mkdir ovtf_venv
    # 创建虚拟环境
    cd ovtf_venv
    python3 -m venv ovtf
    # 激活虚拟环境
    source ovtf/bin/activate

![20211204163137](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204163137.png)

### 3.界面选项与配置

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

### 2.对象检测demo的演示

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

最后一项`--disable_ovtf`参数的意思是不使用openvino进行加速，将使用默认的tf进行推理。

![20211202010502](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211202010502.png)

这里我们首先使用默认的tf进行推理，看看推理用时怎么样。我这里自定义的照片，所以指定了`--input`参数。不加则使用默认提供的。

    python3 examples/object_detection_sample.py --input=/home/kang/200.jpg --disable_ovtf

![20211204163902](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204163902.png)

可以看到推理设备为CPU,推理耗时232.77ms,检测到一个人像。下图是推理完成后保存的照片，左上角显示一些推理信息，矩形框正确的识别并标记了人像。

![20211204164005](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204164005.png)

接着我们使用openvino进行推理加速，看看效果加速怎么样

python3 examples/object_detection_sample.py --input=/home/kang/200.jpg

![20211204164723](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204164723.png)

得到推理结果，推理耗时为185.05ms,缩短了47.72ms，FPS从4提升到了5。我们看到提升还是比较明显的。

![20211204164652](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204164652.png)

### 3.分类检测的demo演示

下载模型文件，并放到example/data路径下。

![20211204170731](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204170731.png)

使用附带的默认示例图像进行推断，不使用ovtf,查看效果

![20211204170805](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204170805.png)

打开ovtf的加速功能，查看推理性能的提升

![20211204170842](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204170842.png)

通过对比可以看到推理时间从48.75ms减少到了35.35ms，缩短了13.5ms,提升显著。之前一直用图片，下图是输入视频流来对比的效果。

![20211204171238](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204171238.png)






