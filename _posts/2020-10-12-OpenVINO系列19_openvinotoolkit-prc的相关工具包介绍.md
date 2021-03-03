---
layout: post
title: oOpenVINO系列19_openvinotoolkit-prc的相关工具包介绍
tag: OpenVINO
---

### 1.工具包简介

为了方便国内人群的使用，我们在码云平台部署了openvinotoolkit-prc项目相关工具包，包括Datumaro、Docker_ci、Dlstreamer_gst、Model_server、Nncf_pytorch、Open_model_zoo、Training_extensions、OpenVINO、Cvat等9个相关的仓库。(参见链接)[https://gitee.com/openvinotoolkit-prc]

### 2.Datumaro

Datumaro全称Dataset Management Framework，是一个数据集管理框架，用于构建，转换和分析数据集的框架和CLI工具，它的安装依赖于python环境。

主要可以做以下几方面的工作：

- 数据集在任何方向上的读取，写入，转换。支持的数据集有：COCO、PASCAL VOC、YOLO、TF Detection API、MOT sequences、MOTS PNG、CVAT、LabelMe。

- 数据集的构建

- 数据集质量检查

- 数据集的比较

- 数据集统计

- 模型整合

关系图如下：

![20201024231546](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20201024231546.png)

### 3.Docker_ci

Docker_ci全称DockerHub CI，该框架可以使用OpenVINO工具包生成Dockerfile、构建、测试和部署镜像。我们可以添加图层并根据需要自定义OpenVINO™的图像，还可以重用可用的Dockerfile。它支持在Linux和Windows的容器中使用OpenVINO手动构建Docker的镜像。需要强调的一点是，容器内的OpenVINO工具包与本地安装在主机上的OpenVINO工具包是相同的，所以官方文档同样适用于Docker版本的OpenVINO。

### 4.Dlstreamer_gst

该存储库是深度学习（DL）Streamer的宿主。DL Streamer是基于GStreamer多媒体框架的流媒体分析框架，用于创建复杂的媒体分析管道。它可确保流水线互操作性，并使用跨英特尔架构CPU、iGPU和英特尔Movidius的OpenVINO Toolkit推理引擎后端的英特尔发行版提供优化的媒体和推理操作。DL Streamer预构建的二进制文件可以与OpenVINO工具包的英特尔发行版一起安装。

### 5.Model Server

OpenVINO模型服务器是可扩展的高性能解决方案，用于服务针对英特尔架构优化的机器学习模型。该服务器通过gRPC（Remote Procedure Calls）端点或REST API提供推理服务--使用与TensorFlow相同的体系结构轻松部署新算法和AI实验，从而为OpenVINO支持的框架中训练的任何模型提供服务。

使用gRPC接口库或falcon REST API框架将服务器实现为python服务，并使用TensorFlow和OpenVINO作为推理执行提供程序对数据进行序列化和反序列化。模型存储库可以驻留在本地可访问文件系统（例如NFS），Google Cloud Storage (GCS), Amazon S3 or MinIO.

一些主要功能：

- 支持多种框架。提供以通用的格式（例如Caffe *，TensorFlow *，MXNet *和ONNX *）训练的模型。
- 部署新的模型版本，而无需更改客户端代码。
- 支持AI加速器，包括Intel Movidius Myriad VPU。可以在Bare Metal Hosts或 Docker容器中启用服务器。
- Kubernetes部署。该服务器可以部署在Kubernetes集群中，从而允许推理服务水平扩展并确保高可用性。
- Sagemaker集成。该服务器支持使用AWS SageMaker容器来提供推理执行。
- 支持多员工配置和并行推理执行。
- 模型重塑。服务器支持在运行时重塑模型。

### 6.Nncf_pytorch

Nncf_pytorch全称Neural Network Compression Framework，该神经网络压缩框架包含基于PyTorch的框架和用于神经网络压缩的样本。它以Python的形式组织，可以在独立模式下构建和使用。框架架构是统一的，可以轻松添加不同的压缩方法。

它的主要特征有：

- 在模型微调过程中应用的各种压缩算法的支持，以实现最佳压缩参数和精度：
>- 量化
>- 二值化
>- 稀疏性
>- 过滤修剪
- 自动，可配置的模型图转换以获得压缩模型。源模型由自定义类包装，并且在图形中插入了其他特定于压缩的图层。
- 压缩方法的通用接口
- GPU加速层可加快压缩模型的微调
- 分布式训练支持
- 每个受支持的压缩算法的配置文件示例。
- 适用于著名第三方存储库（mmdetection，havingface-transformers）的Git补丁，展示了将NNCF集成到自定义训练管道中的过程
- 将压缩模型导出到ONNX检查点，以供OpenVINO™工具包使用。

基本的工作流程:

是加载一个JSON配置脚本，其中包含特定于NNCF的参数，这些参数确定要应用于模型的压缩，然后将模型与配置脚本一起传递给nncf.create_compressed_model函数。此函数返回一个包装好的模型，可以进行压缩微调，并处理该对象，从而可以在训练过程中控制压缩。


### 7.Open_model_zoo

Open_model_zoo包括优化的深度学习模型和一些Demos，以加快高性能深度学习推理应用程序的开发。使用这些免费的预训练模型，而不是训练自己的模型，以加快开发和生产部署过程。

主要组件：

- 预训练模型
- 公开模型说明
- 模型下载器和其他自动化工具
- 通过Deep Learning Deployment Toolkit演示模型用法的Demos
- 用于模型准确性验证的Accuracy Checker工具


### 8.Training_extensions

OpenVINO训练扩展为训练深度学习模型提供了一个便利的环境，并使用OpenVINO工具包对其进行了转换以优化推理

### 9.OpenVINO

OpenVINO是英特尔基于自身现有的硬件平台开发的一种可以加快高性能计算机视觉和深度学习视觉应用开发速度工具套件，主要包括两个组件：模型优化器和推理引擎，以及CPU，GPU和异构插件，以加速在英特尔CPU和英特尔Processor Graphics上的深度学习推理。它支持Open Model Zoo中的预训练模型，以及100多种流行的格式（例如Caffe，TensorFlow，MXNet和ONNX的开源和公共模型。

### 10.Cvat

Cvat全称Computer Vision Annotation Tool，是用于计算机视觉的免费、在线、交互式视频和图像注释工具。




<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)