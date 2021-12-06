---
layout: post
title: talking pre
tag: OpenVINO
---

## 一.Tensorflow介绍

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

## 二.





## OVTF





## OpenVINO Model Server(OVMS)

是一种以C++实现的可扩展的高性能解决方案，用于提供针对英特尔架构优化的机器学习模型。该服务器通过`gRPC`或`REST API`提供推理服务 -- 使用与`TensorFlow Serving`相同的架构轻松部署新算法和AI实验，适用于在OpenVINO支持的框架中训练的任何模型。

服务器使用`TensorFlow Serving API` 和 OpenVINO 作为推理执行提供程序，通过数据序列化和反序列化实现 `gRPC` 和 `REST API` 框架。模型存储库可能驻留在本地可访问的文件系统（例如 NFS）、Google Cloud Storage （GCS）、Amazon S3、Minio 或 Azure Blob Storage 上。


