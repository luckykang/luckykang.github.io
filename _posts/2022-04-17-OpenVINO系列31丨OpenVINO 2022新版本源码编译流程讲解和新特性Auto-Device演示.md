---
layout: post
title: OpenVINO系列31丨OpenVINO 2022新版本源码编译流程讲解和新特性Auto-Device演示
tag: OpenVINO
---

# 一.新版本的介绍

自推出以来OpenVINO一直紧随AI的技术发展，保持着高频的迭代更新，来解决各行各业开发者提出的各种需求和面临的挑战。这次版本迭代，从2021.4到2022.1，改动很大，可以说是迄今为止最重大的更新。

最新版本精简了安装包和运行时库，添加了Auto-Device Plugin、MO参数简化等一系列的有利于开发者快速上手的功能，也官宣了对Paddle Paddle的正式支持，其他还有推理API的改进等等，有兴趣可以查看官网文档，这里不一一而论。

# 二.OpenVINO 安装使用方法有哪些

官网提供的丰富多样的安装方式来满足开发者的使用需求，我们可以通过下载OpenVINO安装包的方式安装，也可以通过PYPI、APT(比如Ubuntu)、YUM(比如Centos)、Anaconda Cloud、Docker、源码编译等方式安装。这里呢介绍一下源码编译怎么安装。

![20220417174306](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220417174306.png)

# 三.环境准备

## 1.支持的linux OS：

Ubuntu* 18.04 (64-bit) with default GCC* 7.5.0

Ubuntu* 20.04 (64-bit) with default GCC* 9.3.0

Red Hat* Enterprise Linux* 8.2 (64-bit) with default GCC* 8.5.0

## 2.软件要求：

CMake* 3.13 or higher

GCC* 7.5 or higher to build OpenVINO Runtime

Python 3.6 or higher for OpenVINO Runtime Python API

[Install Intel® Graphics Compute Runtime for OpenCL™ Driver package 19.41.14441.](https://github.com/intel/compute-runtime/releases/tag/19.41.14441)(可选)

## 3.检查环境

我这里除了Intel® Graphics Compute Runtime都安装好了，如下图环境已经满足编译。

![20220417213117](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220417213117.png)

# 四.下载与编译

## 1.clone代码

从GitHub克隆OpenVINO及其子项目

```
git clone  https://github.com/openvinotoolkit/openvino.git

cd openvino

git submodule update --init --recursive
```

![20220417220910](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220417220910.png)

## 2.切换gitee源（可选）

如果访问github网络不是很顺畅，可以使用gitee源加速

```
cd openvino

chmod +x scripts/submodule_update_with_gitee.sh

./scripts/submodule_update_with_gitee.sh
```

## 3.安装OpenVINO源码编译依赖

```
chmod +x install_build_dependencies.sh

./install_build_dependencies.sh
```

![20220417223402](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220417223402.png)

安装python依赖环境

```
pip3 install -r src/bindings/python/src/compatibility/openvino/requirements-dev.txt

```

![20220417224034](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220417224034.png)

## 4.build选项介绍与操作步骤

### 1.编译选项介绍

cmake 编译OpenVINO 环境的时候，允许我们根据自身需求来灵活指定编译的选项。

如果我们希望有python的环境，可以选择加入下面的选项，要注意的是path要根据自己的实际环境匹配。

```
-DENABLE_PYTHON=ON 

-DNGRAPH_PYTHON_BUILD_ENABLE=ON 

-DPYTHON_EXECUTABLE=`which python3.8` 

-DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.8.so 

-DPYTHON_INCLUDE_DIR=/usr/include/python3.8
```

MKLDNN库，即是Intel公司针对自身CPU以及GPU设计的深度学习加速库，能够成倍地提升神经网络在Intel CPU下的推理速度。

```
-DENABLE_MKL_DNN=ON
```
clDNN，即深度神经网络计算库，是一个用于深度学习(DL)应用程序的开源性能库，旨在对英特尔GPU进行推理的加速。
```
-DENABLE_CLDNN=ON
```

开启ONNX的模型导入功能

```
-DNGRAPH_ONNX_IMPORT_ENABLE=ON

```
开启MYRIAD设备的加速，比如NCS2设备

```
-DENABLE_MYRIAD=ON
```
开启debug功能
```
-DNGRAPH_DEBUG_ENABLE=ON
```
### 2.执行build

介绍完每个选项的功能后，接下来就开始build操作了。

```
mkdir build  && cd build

cmake  -DCMAKE_BUILD_TYPE=Release -DENABLE_PYTHON=ON -DNGRAPH_PYTHON_BUILD_ENABLE=ON -DPYTHON_EXECUTABLE=`which python3.8` -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.8.so -DPYTHON_INCLUDE_DIR=/usr/include/python3.8 -DENABLE_MKL_DNN=ON  -DENABLE_CLDNN=ON -DENABLE_MYRIAD=ON -DNGRAPH_ONNX_IMPORT_ENABLE=ON -DNGRAPH_DEBUG_ENABLE=ON   ..
```
![20220425221908](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425221908.png)


```
make --jobs=$(nproc --all)
```

![20220425221953](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425221953.png)

编译完成

![20220425222044](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425222044.png)
### 3.环境初始化

这里需要把编译好的python动态库导入setupvars路径，这样脚本才能找到python环境。
```
cd scripts/setupvars/

mkdir python

cp -rf  ~/openvino/bin/intel64/Release/lib/python_api/python3.8/    ./python

source ~/openvino/scripts/setupvars/setupvars.sh
```

![20220425222141](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425222141.png)

# 五.Auto-Device 的介绍与演示

## 1.什么是AUTO Device？

AUTO Device (简称 AUTO)是 OpenVINO 工具包中的一个新的特殊“虚拟”或“代理”设备，它不绑定到特定类型的设备。它可以降低使用者对硬件设备选择的复杂性，代码中指定`-d AUTO`后,它可以自动发现设备中可用的计算硬件并自动选择执行推理，通过配置 API 来分别动态优化延迟或吞吐量，从而匹配用户的性能需求。开发人员可以编写一次应用程序并部署到任何地方。

## 2.演示

接下来就让我们试试怎么使用吧。首先检测benchmark_app的环境是否配置成功。这里发现有个报错，当前环境没有'progress'模块。
```
cd openvino/tools/benchmark_tool

./benchmark_app.py --help
```

![20220425222305](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425222305.png)


安装progress模块
```
pip3 install progress
```

![20220425222329](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425222329.png)

再次执行上边的命令，现在可以看到benchmark_app的参数介绍了，环境配置成功。它也检测到我们目前可用的device有CPU、GNA和GPU

![20220425222349](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425222349.png)

找个resnet-50的OV模型，执行推理。可以在执行过程中看到`Device info`为 `AUTO`,此时OV会在可用的硬件设备中自动匹配并加载和优化模型，进行推理。

```
./benchmark_app.py -m ~/resnet-50-pytorch.xml  -d AUTO -b 8 -t 50 -nstreams 8 -progress
```
![20220425224614](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220425224614.png)