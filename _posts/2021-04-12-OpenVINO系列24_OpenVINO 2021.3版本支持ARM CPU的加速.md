---
layout: post
title: OpenVINO系列24_OpenVINO 2021.3版本支持ARM CPU的加速
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

### 3.编译以及遇到的问题

#### 方法1：使用预设的Dockerfile构建OpenCV，OpenVINO和插件

- 克隆openvino_contrib存储库：

```
git clone --recurse-submodules --single-branch --branch=master https://github.com/openvinotoolkit/openvino_contrib.git 
```

- 转到插件目录：

```
cd openvino_contrib/modules/arm_plugin
```

- 构建Docker image：

```
docker image build -t arm-plugin -f Dockerfile.RPi32 .
```

编译过程共有15步，我在执行到第七步的时候报错了，如图：

![2021-04-02-14-13-41](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-04-02-14-13-41.png)

然后根据报错从key-server匹配给定密钥ID的key,但是发现还是无法解决这个问题。

![2021-04-02-14-31-22](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-04-02-14-31-22.png)

#### 方法2：使用Docker 构建OpenVINO和不具有OpenCV的插件

- 克隆openvino和openvino_contrib存储库：

```
git clone --recurse-submodules --single-branch --branch=master https://github.com/openvinotoolkit/openvino.git 

git clone --recurse-submodules --single-branch --branch=master https://github.com/openvinotoolkit/openvino_contrib.git 
```

- 构建ie_cross_armhf

创建一个`ie_cross_armhf`文件夹，并在里面添加一个Dockerfile，包含以下内容：

```
FROM debian:stretch

USER root

RUN dpkg --add-architecture armhf && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
    build-essential \
    crossbuild-essential-armhf \
    git \
    wget \
    libusb-1.0-0-dev:armhf \
    libgtk-3-dev:armhf \
    libavcodec-dev:armhf \
    libavformat-dev:armhf \
    libswscale-dev:armhf \
    libgstreamer1.0-dev:armhf \
    libgstreamer-plugins-base1.0-dev:armhf \
    libpython3-dev:armhf \
    python3-pip \
    python-minimal \
    python-argparse

RUN wget https://www.cmake.org/files/v3.14/cmake-3.14.3.tar.gz && \
    tar xf cmake-3.14.3.tar.gz && \
    (cd cmake-3.14.3 && ./bootstrap --parallel=$(nproc --all) && make --jobs=$(nproc --all) && make install) && \
    rm -rf cmake-3.14.3 cmake-3.14.3.tar.gz

RUN git config --global user.name "Your Name" && \
    git config --global user.email "you@example.com"
```
#### 注意：上面代码的user.name和user.email要替换为自己的。它使用Debian Stretch（Debian 9）操作系统进行编译，因为它是Raspbian Stretch的基础。

- 构建Docker image

```
docker image build -t ie_cross_armhf ie_cross_armhf
```

![20210412163823](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412163823.png)

- 运行同时挂载openvino和openvino_contrib存储库的Docker容器

```
docker container run --rm -it -v /absolute/path/to/openvino:/openvino -v /absolute/path/to/openvino_contrib:/openvino_contrib ie_cross_armhf /bin/bash 
```

![20210412164302](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412164302.png)

- 在容器中安装scons
  
```
apt-get install scons
```

![20210412164407](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412164407.png)

- 转到openvino目录,准备一个构建文件夹

```
cd openvino
mkdir build && cd build
```
- 使用ARM插件构建OpenVINO

```
 cmake -DCMAKE_BUILD_TYPE=Release \
       -DCMAKE_TOOLCHAIN_FILE="../cmake/arm.toolchain.cmake" \
       -DTHREADING=SEQ \
       -DIE_EXTRA_MODULES=/openvino_contrib/modules \
       -DBUILD_java_api=OFF .. && make
```

#### 遇到了报错，说编译失败，container中的openvino文件夹中没CMakeLists.txt

![20210412164637](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412164637.png)

我查看了openvino文件夹，居然是空目录。所以第二种方法也尝试失败了。

![20210412165331](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412165331.png)

#### 方法3：在ARM平台上构建OpenVINO和不带OpenCV的插件（本机编译）

- 安装必要的构建依赖项：

```
sudo apt-get update  
sudo apt-get install -y git cmake  scons build-essential
```

![20210412165612](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412165612.png)

- 克隆openvino_contrib存储库：

```
git clone --recurse-submodules --single-branch --branch=master https://github.com/openvinotoolkit/openvino_contrib.git 
```

- 转到插件目录,准备一个build文件夹：

```
cd openvino_contrib/modules/arm_plugin
mkdir build && cd build
```

- 构建插件：

#### 注意：`<path to OpenVINO package build folder>` 要替换为自己相应的路径

```
cmake -D InferenceEngineDeveloperPackage_DIR=<path to OpenVINO package build folder> -DCMAKE_BUILD_TYPE=Release .. && make
```
我们也可以使用 `-DARM_COMPUTE_SCONS_JOBS=N` 参数来通过N个同时执行的构建工作来加快ArmCompute库的构建。


### 附录

项目地址：[https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin](https://github.com/openvinotoolkit/openvino_contrib/tree/master/modules/arm_plugin)

