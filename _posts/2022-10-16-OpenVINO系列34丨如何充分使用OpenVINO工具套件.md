---
layout: post
title: OpenVINO系列34丨如何充分使用OpenVINO工具套件
tag: OpenVINO
---

# 一.OpenVINO的流程使用介绍

OpenVINO工具套件中已经包含了最新优化好的OpenCV、OpenVX版本，还包含了用于加速视频编程、解码的Media SDK,现在Media SDK正在向OpenVPL进行迁移。值得一提的是FPGA的软件包，只会根据需求直接提供，将会从正式版本中慢慢分离出来。

最重要的深度学习部署工具套件DLDT用于深度学习的推理加速。作为模型优化器和推理引擎的工具套件的核心，是我们必须要熟悉和掌握的内容。其中模型优化器是对模型进行推理之前进行预处理的工具，输出基于原始模型的IR文件，这个文件仅能在OpenVINO中使用。而推理引擎就是使用IR文件在代码编写的应用中执行深度学习推理请求，只需要一套代码就可以部署在多种设备中。模型优化器的功能不仅仅是单一的转换模型网络为IR格式，它还可以在转换过程中同时对神经网络执行压缩网络、裁剪网络、转换格式（从FP32转换为FP16）等各种优化。推理引擎也有很多可选项参数可以设置。

![20221016132405](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20221016132405.png)

# 二.demo演示

## 1.3D human pose demi

