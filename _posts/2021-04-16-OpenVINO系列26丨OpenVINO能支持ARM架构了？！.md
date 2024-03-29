---
layout: post
title: OpenVINO系列26丨OpenVINO能支持ARM架构了？！
tag: OpenVINO
---

>>> 全文字数635，预计阅读4min

### 1.扩展模块的介绍

以前，推理引擎仅支持Intel硬件：CPU，GPU，FPGA和VPU。最近，一个新的扩展模块`openvino_contrib`已在GitHub上发布，它是OpenVINO开源版本的一部分，不包含在OpenVINO工具包的英特尔发行版中。主要包括三部分：arm_plugin、java_api、mo_pytorch，每部分都是解耦的，可以独立编译使用。本文主要介绍`arm_plugin`插件的编译使用。该插件允许推理引擎使用ARM Compute Library作为后端在ARM CPU上运行DL网络。


### 2.支持的平台

OpenVINO ARM CPU插件在以下平台上受支持和验证：

硬件 | 系统 | 
- | - 
树莓派4B | Debian 10.3 (32-bit)
树莓派4B | Ubuntu 18.04 (64-bit)

### 3.编译构建

- 编译方法和使用环境：

github文档提供了三种方法(文档链接见文章末尾`Q&A`)。我这里使用了第一种交叉编译的方式，在ubuntu18.04系统上Build Dockerfile来构建OpenVINO、OpenCV和ARM CPU Plugin。

- 克隆openvino_contrib存储库

```
git clone --recurse-submodules --single-branch --branch=master https://github.com/openvinotoolkit/openvino_contrib.git 
```

- 转到ARM_Plugin目录

```
cd openvino_contrib/modules/arm_plugin
```

- Build Docker Image

Build过程由`/armplg_build.sh`在armcpu_plugin路径执行脚本，共有15步。

```
docker image build -t arm-plugin -f Dockerfile.RPi32 .
```

![20210415174905](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415174905.png)

- Build the plugin in Docker container

所有的中间结果和build artifacts都保存在工作路径下，
因此，我们可以挂载整个工作目录以将所有结果存储在容器外部。

```
mkdir build
docker container run --rm -ti -v $PWD/build:/armcpu_plugin arm-plugin
```

完成后如图所示

![20210415175048](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175048.png)

查看目录包含哪些文件

![20210415175232](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175232.png)


- 上述操作耗时近2小时，生成的build文件2.4GB。我们可以只导出带有artifacts的归档文件`OV_ARM_package.tar.gz`,比较节约时间，只有108MB，即执行下面命令：


```
docker container run --rm -ti --tmpfs /armcpu_plugin:rw -v $PWD:/remote \
                     arm-plugin sh -c "sh /armplg_build.sh && cp ./OV_ARM_package.tar.gz /remote"
```


![20210415175352](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175352.png)


### 4.运行加速

- 把编译生成的包`OV_ARM_package.tar.gz`拷贝到树莓派4B上
，并解压。

![20210415180556](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415180556.png)


- 我这里提前准备了优化好的IR文件,并拷贝到树莓派4B上，这样就可以直接在模型中加载。

```
vehicle-license-plate-detection-barrier-0106.xml
vehicle-license-plate-detection-barrier-0106.bin
```

- 转到推理引擎bin目录

```
cd /home/pi/deployment_tools/inference_engine/bin/armv7l
```
- 下载车辆图像

```
wget https://raw.githubusercontent.com/openvinotoolkit/openvino/master/scripts/demo/car_1.bmp
```

- 将OpenCV和OpenVINO库目录添加到LD_LIBRARY_PATH

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/opencv/lib/:~/deployment_tools/inference_engine/lib/armv7l/
```

![2021-04-15-013301_1541x50_scrot](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-04-15-013301_1541x50_scrot.png)

- 在ARM平台上运行对象检测demo

```
./object_detection_sample_ssd -m ~/FP32/vehicle-license-plate-detection-barrier-0106.xml -i car_1.bmp -d CPU
```

![2021-04-15-013301-4234](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-04-15-013301-4234.gif)


- 查看使用ARM CPU加速推理后的输出图像

![20210415175909](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175909.png)


### Q&A：

> 1.请问项目在哪里可以看到？

Gitee(推荐):
[https://gitee.com/openvinotoolkit-prc/openvino_contrib#/openvinotoolkit-prc/openvino_contrib/blob/master/modules/arm_plugin](https://gitee.com/openvinotoolkit-prc/openvino_contrib#/openvinotoolkit-prc/openvino_contrib/blob/master/modules/arm_plugin)

Github:
[https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin](https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin)

> 2.请问编译阶段可以在ARM平台执行吗？

由于文档没有详细说明，编译阶段我尝试在树莓派4B上build,但是发现各种莫名其妙的问题，在此建议编译在x86架构的硬件上进行。

> 3.请问文章所用的IR文件和编译生成的tar包哪里可以下载，可以跳过编译阶段直接用tar包进行推理吗？

IR文件和tar包我已经上传到百度云网盘，可以扫码获取。可以跳过编译阶段，直接把tar包拷贝到ARM平台加载模型进行推理。

![WechatIMG18](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/WechatIMG18.jpeg)

> 4.请问哪里可以下载文章所说的树莓派的系统?

raspberrypi(Debian 10)下载地址：

[https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit](https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit)

Ubuntu Mate(18.04) 下载地址：

[https://ubuntu-mate.org/download/amd64/](https://ubuntu-mate.org/download/amd64/)

