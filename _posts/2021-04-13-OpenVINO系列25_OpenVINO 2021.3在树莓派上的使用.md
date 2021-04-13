---
layout: post
title: OpenVINO系列25_OpenVINO 2021.3在树莓派上的使用
tag: OpenVINO
---

### 1.介绍说明

Raspbian OS的OpenVINO工具包包括推理引擎和MYRIAD插件。我们可以将其与插入USB端口之一的Intel Neural Compute Stick 2一起使用。

### 2.环境支持

系统：适用于32位Raspbian 操作系统

硬件：具有ARMv7-A CPU架构的树莓派和NCS2(检查是否`uname -m`返回`armv7l`)。

软件：

CMake * 3.7.2或更高

Python * 3.5，32位


### 3.包含组件

- 推理引擎：对中间表示（优化模型）进行推理以生成推理结果的软件库。

- MYRIAD插件：该插件是为在Intel Neural Compute Stick 2上推理神经网络而开发的。

**注意：用于Raspberry 的OpenVINO软件包不包含Model Optimizer。要将模型转换为中间表示（IR），需要将其单独安装到主机上，也不包含Open Model Zoo演示应用程序，如果需要可以单独下载。**

### 4.安装openvino

- 下载l_openvino_toolkit_runtime_raspbian_p_<version>.tgz

[https://storage.openvinotoolkit.org/repositories/openvino/packages/](https://storage.openvinotoolkit.org/repositories/openvino/packages/)

- 创建安装文件夹

```
sudo mkdir -p /opt/intel/openvino
```
- 解压到相应位置

```sudo tar -xf  l_openvino_toolkit_runtime_raspbian_p_<version>.tgz --strip 1 -C /opt/intel/openvino```


- 安装外部依赖

```sudo apt install cmake```

- 设置环境变量

```echo "source /opt/intel/openvino_2021/bin/setupvars.sh" >> ~/.bashrc```

### 5.添加NCS2驱动

- 把用户加入用户组

```sudo usermod -a -G users "$(whoami)"```

**然后重启系统**

- 安装驱动

```sh /opt/intel/openvino/install_dependencies/install_NCS_udev_rules.sh```

- 插入NCS 2计算棒

### 6.编译运行

- 在home目录创建文件夹

```mkdir build && cd build```

- 构建构建对象检测sample

```
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-march=armv7-a" /opt/intel/openvino_2021/deployment_tools/inference_engine/samples/cpp

make -j2 object_detection_sample_ssd
```

- 使用模型下载器下载经过预先​​训练的人脸检测模型

```
git clone --depth 1 https://github.com/openvinotoolkit/open_model_zoo
cd open_model_zoo/tools/downloader
python3 -m pip install -r requirements.in
python3 downloader.py --name face-detection-adas-0001 
```

- 指定模型和输入图像的路径来运行

```
./armv7l/Release/object_detection_sample_ssd -m face-detection-adas-0001.xml -d MYRIAD -i <path_to_image>
```


### 附录

项目地址：[https://docs.openvinotoolkit.org/latest/openvino_docs_install_guides_installing_openvino_raspbian.html](https://docs.openvinotoolkit.org/latest/openvino_docs_install_guides_installing_openvino_raspbian.html)