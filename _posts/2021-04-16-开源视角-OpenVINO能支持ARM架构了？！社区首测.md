---
layout: post
title: 开源视角-OpenVINO能支持ARM架构了？！社区首测
tag: OpenVINO
---

### 1.扩展模块的介绍

OpenVINO刚不久发布了新版本2021.3，其中增加了一个扩展模块，但是由于不太稳定，并未放到OpenVINO的发行版中，不过可以单独编译使用。开发OpenVINO ARM CPU插件是为了使OpenVINO API在ARM CPU上启用深度神经网络推理。该插件使用ARM Compute Library 作为后端。

### 2.支持的平台

OpenVINO ARM CPU插件在以下平台上受支持和验证：

硬件 | 系统 | 
- | - 
树莓派4B | Debian 10.3 (32-bit)
树莓派4B | Ubuntu 18.04 (64-bit)

### 3.编译构建

**文档提供三种方法，文档链接见附录。我这里使用了交叉编译的方式，build dockerfile来构建OpenCV, OpenVINO和plugin。**

我找了一台装有ubuntu18.04的机器，通过build Dockerfile，在容器中构建OpenVINO、OpenCV和ARM CPU plugin。

- 克隆openvino_contrib存储库

```
git clone --recurse-submodules --single-branch --branch=master https://github.com/openvinotoolkit/openvino_contrib.git 
```

- 转到arm_plugin目录

```
cd openvino_contrib/modules/arm_plugin
```

- build Docker image

build过程由`/armplg_build.sh`在armcpu_plugin路径执行脚本，共有15步。
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

完成后如图所示，不过该操作耗时近2个小时，生成的build文件2.4GB。

![20210415175048](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175048.png)

查看目录包含哪些文件

![20210415175232](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175232.png)


- 为了节省时间，我们也可以只导出带有artifacts的归档文件`OV_ARM_package.tar.gz`,只有108MB，即执行下面命令：
```
docker container run --rm -ti --tmpfs /armcpu_plugin:rw -v $PWD:/remote \
                     arm-plugin sh -c "sh /armplg_build.sh && cp ./OV_ARM_package.tar.gz /remote"
```

![20210415175352](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175352.png)


### 4.运行加速

- 把编译生成的包`OV_ARM_package.tar.gz`拷贝到树莓派4上
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


- 查看使用ARM CPU加速推理后的图像

![20210415175909](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210415175909.png)


### 附录

项目地址：[https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin](https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin)

