---
layout: post
title: talking pre
tag: OpenVINO
---

## 前言


### 1.什么是Tensorflow

TensorFlow 是一个开源的、基于 Python 的机器学习框架，它由 Google 开发，并在图形分类、音频处理、推荐系统和自然语言处理等场景下有着丰富的应用，是目前很热门的机器学习框架。

### 2.Tensorflow特点

- 支持所有流行语言，如 Python、C++、Java、R和Go。
- 可以在多种平台上工作，甚至是移动平台和分布式平台。
- 它受到所有云服务（AWS、Google和Azure）的支持。
- Keras——高级神经网络 API，已经与 TensorFlow 整合。
- 与 Torch/Theano 比较，TensorFlow 拥有更好的计算图表可视化。
- 允许模型部署到工业生产中，并且容易使用。
- 有非常好的社区支持。
- TensorFlow 不仅仅是一个软件库，它是一套包括 TensorFlow，TensorBoard 和 TensorServing 的软件。

### 3.应用场景

在国外，Airbnb 用 TensorFlow 做图片分类。 房东上传家里的图片，系统自动把图片分类成客厅、厨房和卧室，让租客在搜索时可以更简单。

在国内，闲鱼在上传照片自动打标签这个场景中借助了 TensorFlow 的能力。

## page08

Human Pose Estimation 人体姿态评估

Object Recognition 对象识别

Instance Segmentation 实例分割

Gesture Recognition 手势识别

## page9

是一个离线工具，不需要连接网络就可以运行

## page10

优化网络图层，减少推理时间。预训练模型包含一些对于训练很重要的图层，但在执行推理阶段无用，有可能增加推理时间，通过优化可以精简图层

**Linear Operations Fusing 线性运算融合**

许多卷积神经网络包括批量归一化和标量移码层（例如，Resnet * ，Inception *） ，它们可以表示为一系列线性运算： 加法和乘法。例如 ScaleShift 层可以表示为 Mul → Add 序列。这些层可以融合到以前的卷积或全连接层，除非卷积在添加操作之后(由于卷积填充)。

**ResNet optimization 步幅优化**

ResNet 优化是一种特定的优化，适用于 Caffe ResNet 拓扑（如 ResNet50、ResNet101、ResNet152）和基于 ResNet 的拓扑

**Grouped Convolution Fusing 分组卷积融合**

分组卷积融合是一种适用于 TensorFlow 拓扑的特定优化。这个优化的主要思想是结合split输出的卷积结果，然后使用 Concat 操作按照与split输出相同的顺序重新组合它们

[https://docs.openvino.ai/latest/openvino_docs_MO_DG_prepare_model_Model_Optimization_Techniques.html#grouped-convolution-fusing](https://docs.openvino.ai/latest/openvino_docs_MO_DG_prepare_model_Model_Optimization_Techniques.html#grouped-convolution-fusing)

## page11

多设备插件会自动将推理请求分配给可用的计算设备，以并行执行请求。多设备插件的好处包括：

使用多个设备提高了吞吐量（与单个设备执行相比）

更一致的性能，因为设备分担推理负担（如果一个设备太忙，另一个设备可以承担更多的负载。

[https://docs.openvino.ai/latest/openvino_docs_IE_DG_supported_plugins_MULTI.html](https://docs.openvino.ai/latest/openvino_docs_IE_DG_supported_plugins_MULTI.html)

## page14

本文介绍了 OpenVINO™ integration with TensorFlow 的高级架构。该功能注册为 TensorFlow 中的graph Optimization Pass，并使用 OpenVINO优化所支持的算子集群的执行。不支持的算子集群返回至原生 TensorFlow 运行时。

**Operator Capability Manager**

Operator Capability Manager (OCM) 对 TensorFlow 算子实施几项检查，以确定 OpenVINO后端是否支持该算子。检查包括支持的算子类型、数据类型、属性值、输入输出节点和其他条件。这些检查的实施基于数千项算子测试和模型测试的结果。随着我们将更多算子测试和模型测试添加到测试基础设施中，OCM 将持续演进和发展。这是一个重要模块，它决定模型中的哪些层前往 OpenVINO™ 后端，哪些层返回 TensorFlow 原生运行时。OCM 将 TensorFlow 图作为输入，返回一个可标记进行集群化的算子列表，以便这些算子在 OpenVINO™ 后端中运行。

**Graph Partitioner**

Graph partitioner 检查 OCM 标记的节点，并对其进行进一步分析。在这一阶段，标记的算子首先分配给集群。一些集群会在分析之后被删除掉。例如，如果集群很小，或者在接收较多上下文后，集群不被后端支持，那么该集群将被删除，算子返回原生 TensorFlow 运行时。之后，每个算子集群都被封装到自定义算子中，在 OpenVINO™ 运行时中执行。

**TensorFlow Importer**

TensorFlow importer 通过用于 OpenVINO工具套件的最新算子集将集群中的 TensorFlow 算子解析为 OpenVINO™ nGraph 算子。nGraph 函数专门用于各个集群。该函数创建后，将被封装到 OpenVINO™ CNNNetwork 中，该网络包含将在 OpenVINO后端执行的集群的中间表示。

**Backend Manager**

Backend manager 创建一个后端，用于执行 CNNNetwork。我们实施了两类后端。

- 基础 后端
- VAD-M 后端
- 
基础 后端用于英特尔® CPU、英特尔® 集成 GPU 和英特尔® Movidius™ 视觉处理单元 (VPU)。后端创建推理请求并在对指定的输入数据运行推理。

VAD-M 后端用于支持 8 颗英特尔 Movidius™ MyriadX VPU 的英特尔® 视觉加速器设计（称作 VAD-M 或 HDDL）。我们支持在 VAD-M 后端执行批量推理。用户提供批量输入后，将会创建多个推理请求，且推理在 VAD-M 中所有的可用 VPU 中并行运行。

后端管理器支持动态回退，这意味着如果相应的 CNNNetwork 在 OpenVINO™ 执行失败时，则回退到原生 TensorFlow 运行时去执行。

[https://github.com/openvinotoolkit/openvino_tensorflow/blob/master/docs/ARCHITECTURE.md](https://github.com/openvinotoolkit/openvino_tensorflow/blob/master/docs/ARCHITECTURE.md)














## OVTF





## OpenVINO Model Server(OVMS)

是一种以C++实现的可扩展的高性能解决方案，用于提供针对英特尔架构优化的机器学习模型。该服务器通过`gRPC`或`REST API`提供推理服务 -- 使用与`TensorFlow Serving`相同的架构轻松部署新算法和AI实验，适用于在OpenVINO支持的框架中训练的任何模型。

服务器使用`TensorFlow Serving API` 和 OpenVINO 作为推理执行提供程序，通过数据序列化和反序列化实现 `gRPC` 和 `REST API` 框架。模型存储库可能驻留在本地可访问的文件系统（例如 NFS）、Google Cloud Storage （GCS）、Amazon S3、Minio 或 Azure Blob Storage 上。


