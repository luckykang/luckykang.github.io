---
layout: post
title: 开源视角-OpenVINO能支持ARM架构了？！社区首测
tag: OpenVINO
---

>>> 全文字数537，预计阅读2min

### 1.扩展模块的介绍

OpenVINO诞生至今，其功能越来越强，模块也越来越多。为了保证发布版本的稳定性，Github上新增了`openvino_contrib`代码库用来开发扩展模块，功能稳定后再合并到OpenVINO库中。扩展模块中的每个模块都是解耦的，可以独立编译使用。主要包括三部分：arm_plugin、java_api、mo_pytorch。本文主要介绍arm_plugin模块的使用。`arm_plugin`使用OpenVINO API在ARM CPU上启用深度神经网络推理。该插件使用ARM Compute Library作为后端。

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

> 1.项目地址：[https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin](https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin)

> 2.本文所用的IR文件和编译生成的tar包扫码可下载：

![webwxgetmsgimg](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/webwxgetmsgimg.png)

> 3.由于文档没有详细说明，编译阶段我使用过适合树莓派安装的raspbarrypi、ubuntu mate系统进行build,都发现了一些报错，在此建议编译在x86架构的硬件上进行。


