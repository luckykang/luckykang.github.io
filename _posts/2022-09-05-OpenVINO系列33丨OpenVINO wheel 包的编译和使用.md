---
layout: post
title: OpenVINO系列33丨OpenVINO wheel 包的编译和使用
tag: OpenVINO
---

# 一.安装和设置docker

## 1.下载运行ubuntu images

```
sudo docker pull ubuntu:20.04
sudo docker run -it ubuntu:20.04
```

![20220904124756](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904124756.png)

## 2.在 Docker 中安装 git 和 sudo，并将目录更改为 /opt。

```
apt update
apt install git sudo
cd /opt
```
![20220904151900](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904151900.png)

# 二.克隆 OpenVINO 的代码仓和子代码仓

```
git clone https://github.com/openvinotoolkit/openvino.git
cd openvino
git checkout   9350914 -b  ww02 
git submodule update --init --recursive
```

![20220904154347](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904154347.png)

# 三.安装构建(Build)依赖项

```
./install_build_dependencies.sh
```
到这一步会提示选择地区和时区，分别填入`6`和`70`,回车则会继续安装

![20220904223151](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904223151.png)

执行完成

![20220904224614](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904224614.png)


```
apt install cython3
pip3 install --upgrade pip
pip3 install -r src/bindings/python/wheel/requirements-dev.txt
# 如果需要支持GPU，则必须为 OpenCL 安装计算运行依赖。
apt install intel-opencl-icd
```

![20220904225714](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904225714.png)

# 四.编译源代码

```
mkdir build && cd build

cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DENABLE_DEBUG_CAPS=ON \
  -DENABLE_WHEEL=ON \
  -DENABLE_CPPLINT=OFF \
  -DENABLE_GNA=OFF \
  -DENABLE_INTEL_CPU=ON \
  -DENABLE_INTEL_GPU=ON \
  -DENABLE_VPU=OFF \
  -DENABLE_OPENCV=ON \
  -DENABLE_MKL_DNN=OFF \
  -DENABLE_CLDNN=ON \
  -DENABLE_SAMPLES=ON \
  -DENABLE_TESTS=OFF \
  -DBUILD_TESTS=OFF \
  -DTREAT_WARNING_AS_ERROR=ON \
  -DNGRAPH_WARNINGS_AS_ERRORS=ON \
  -DNGRAPH_COMPONENT_PREFIX=deployment_tools/ngraph/ \
  -DNGRAPH_UNIT_TEST_ENABLE=OFF \
  -DNGRAPH_TEST_UTIL_ENABLE=OFF \
  -DENABLE_PYTHON=ON \
  -DPYTHON_EXECUTABLE=/usr/bin/python3.8 \
  -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.8.so \
  -DPYTHON_INCLUDE_DIR=/usr/include/python3.8 \
  ../

```
![20220904230332](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904230332.png)

运行以最大线程数运行 Make

```
make --jobs=$(nproc --all)
```
![20220904230946](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904230946.png)

编译完成后，可以在“build”文件夹下的“wheels”目录中找到 wheel 包，既有 OpenVINO™ Runtime 包也有 OpenVINO™ development 包

![20220904231011](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904231011.png)

先按 Ctrl+P，再按 Ctrl+Q 退出容器而不停止它；接着运行 docker ps 以获取容器的 id ，并通过容器 id 将容器中的 wheel 文件复制到您的主机。

![20220904231311](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904231311.png)


```
docker ps -a
docker cp a88e3ab18800:/opt/openvino/build/wheels/openvino_dev-2022.3.0-000-py3-none-any.whl .
docker cp a88e3ab18800:/opt/openvino/build/wheels/openvino-2022.3.0-000-cp38-cp38-manylinux_2_31_x86_64.whl .
```

![20220904231613](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904231613.png)

# 五.在目标机器上使用最新的 OpenVINO™ wheel 安装包 

```
python3.8 -m venv  env
source env/bin/activate
pip install --upgrade pip
pip install /home/gta/openvino/build/dldt/wheels/openvino-2022.3.0-000-cp38-cp38-manylinux_2_31_x86_64.whl
pip install /home/gta/openvino/build/dldt/wheels/openvino_dev-2022.3.0-000-py3-none-any.whl
```

![20220904232229](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904232229.png)


检测环境

```
benchmark_app  --help
```

![20220904232829](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904232829.png)

执行成功
```
benchmark_app -m /home/gta/kang/ov_models/ssd_mobilenet_v1_coco.xml -d GPU -hint none -t 50  -b 1 -nstreams 8
```

![20220904233057](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220904233057.png)

ov_build_wiki：
[https://github.com/openvinotoolkit/openvino/wiki/BuildingForLinux](https://github.com/openvinotoolkit/openvino/wiki/BuildingForLinux)
