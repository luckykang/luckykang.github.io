---
layout: post
title: OpenVINO系列30丨OpenVINO_Notebooks介绍与使用
tag: OpenVINO
---

## 一.概述

该项目包含一些可以直接运行的Jupyter notebooks，方便我们学习和使用openvino，通过这些提供的sample,我们可以清楚的了解openvino的工作原理，以及如何更轻松的在我们的深度学习程序中用openvino优化推理。

## 二.系统要求

运行OpenVINO Notebooks需要预装Python和Git，建议在python虚拟环境中使用。

![20211204211115](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204211115.png)

## 三.了解Notebooks

### 1.clone仓库到本地

    git clone https://github.com/openvinotoolkit/openvino_notebooks.git

![20211204212940](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204212940.png)

### 2.Notebooks功能分区

![Snipaste_2021-12-04_23-30-35](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/Snipaste_2021-12-04_23-30-35.png)

### 3.notebooks demo的功能说明

Notebook | 姓名 | 
- | - 
001-hello-world | 14行代码实现视觉分类检测应用 
002-openvino-api|Openvino python api介绍
003-hello-segmentation|基于Openvino的视觉语义分割应用
004-hello-detection|基于Openvino的文字识别应用
101-tensorflow-to-openvino|基于Tensorflow预训练模型，实现分类检测部署	
102-pytorch-onnx-to-openvino|基于Pytorch预训练模型，实现语义分割部署	
103-paddle-onnx-to-openvino|基于PadlePadle预训练模型，实现分类检测部署	
104-model-tools|Openvino模型的下载与评估	
105-language-quantize-bert|BERT预训练模型的优化与量化
201-vision-monodepth|单目深度检测应用实现	
202-vision-superresolution-image|图像超分辨率应用实现
202-vision-superresolution-video|视频超分辨率应用实现	
205-vision-background-removal|图像背景替换的应用实现	
206-vision-paddlegan-anime|利用paddleGAN的图片风格转换的应用实现
207-vision-paddlegan-superresolution|利用paddleGAN的图像超分辨率应用实现	
208-optical-character-recognition|文字识别应用实现
301-tensorflow-training-openvino|利用Tensorflow及OpenVINO 实现从模型训练到优化部署的完整流程	
301-tensorflow-training-openvino-pot|基于POT工具的模型量化	
302-pytorch-quantization-aware-training|基于NNCF工具的模型压缩	
402-pose-etimation-webcam|基于openvino人体姿态评估	

## 四.获取与启动Notebooks

### 1.创建虚拟环境

![20211204212903](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204212903.png)

### 2.更新pip、安装环境依赖

    python3 -m pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple
    cd openvino_notebooks
    pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

### 3.用ipykernel创建运行环境的专用内核,并指定名称

    python3 -m ipykernel install --user --name openvino_env

![20211204214005](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204214005.png)

### 4.启动单个Notebooks，比如201-vision-monodepth

    jupyter notebook notebooks/201-vision-monodepth/201-vision-monodepth.ipynb

![20211204214236](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204214236.png)

### 5.启动所有Notebooks

    jupyter lab notebooks

![20211204231027](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204231027.png)

## 五.101-tensorflow-to-openvino

这个notebook介绍了如何把TensorFlow 模型转换为OpenVINO IR文件。我们把一个TensorFlow MobilenetV3模型转换为IR中间表示，并使用 OpenVINO 的推理引擎对图像进行分类。

### 1.导入模块

`pathlib`是用于处理文件路径的模块，是面向对象的，简化了操作，相比os更加简单好用。

![20211205220339](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205220339.png)

### 2.设置模型存放路径

指定原模型所在路径和转化为OpenVINO IR中间表示的路径，这里使用的模型是`001 hello-world `notebook中的 `mobileenetv3`

![20211205220506](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205220506.png)

### 3.指定模型优化器的转换参数

使用OpenVINO的模型优化器进行转换，输入模型相关shape和路径，添加了`--mean_values`、
`--scale_values`等参数，精度指定了为FP16。

![20211205220756](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205220756.png)

### 4.执行模型优化

执行完成后，我们可以在model目录下看到生成的IR文件。

![20211205221028](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205221028.png)

### 5.推理验证

加载模型文件，指定推理硬件为CPU。获取模型信息，加载要推理的图片并处理为适合的格式

![20211205223725](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205223725.png)

执行推理并输出结果

![20211205225308](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205225308.png)

### 6.性能测试

测试一千张图片的推理用时，来评估模型优化后的性能。如果我们想得到更准确更专业的性能参数，可以
使用benchmark_app进行性能评估。

![20211205230852](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211205230852.png)

到此，从模型的优化到推理，再到简单的性能测试，就都完成啦。

## 六.301-tensorflow-training-openvino

这个notebook介绍了如何使用TensorFlow和OpenVINO从训练到部署的流程。下图可以看到两个ipynb文件,我们分为两部分来说。

`301-tensorflow-training-openvino.ipynb`是端到端的深度学习训练教程，演示如何使用 TensorFlow 和 OpenVINO 训练、转换和部署图像分类模型。它使用了`tf _ flowers`数据集,约3700张鲜花照片。

`301-tensorflow-training-openvino-pot.ipynb`演示如何对上面OpenVINO IR 模型进行量化。量化过程是使用OpenVINO Post Training Optimization Tool (POT)
工具实现的，通过量化可以加快模型的推理速度。

![20211206225859](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211206225859.png)

首先来看第一部分：如何对花的图像进行分类（基于TensorFlow的官方教程）。它使用`keras.Sequential`模型创建了一个图像分类器，并使用p`reprocessing.image_dataset_from_directory`加载数据。

### 1.导入模块

`PIL`,全称 `Python Imaging Library`，是 Python 平台一个功能非常强大而且简单易用的图像处理库。

![20211206235230](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211206235230.png)

### 2.



