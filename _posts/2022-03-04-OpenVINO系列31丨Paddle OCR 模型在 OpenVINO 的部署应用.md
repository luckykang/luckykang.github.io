---
layout: post
title: OpenVINO系列32丨Paddle OCR 模型在 OpenVINO 的部署应用
tag: OpenVINO
---
# 一.介绍与特性

## 1. PaddleOCR 与 OpenVINO 介绍

PaddleOCR 是百度开源的超轻量级 OCR模型库，提供了数十种文本检测、识别模型，旨在打造一套丰富、领先、实用的文字检测、识别模型/工具库，助力使用者训练出更好的模型，并应用落地。

OpenVINO 是用于优化和部署 AI 推理的开源工具包，提升计算机视觉、自动语音识别、自然语言处理和其他常见任务中的深度学习性能。

OpenVINO 对 paddlepaddle 的原生支持计划于 OpenVINO 2022.1版本发布。我们要做的是将 PaddlePaddle 的模型传递给 OpenVINO 直接进行推理，而无需先用 ModelOptimizer 转换成 IR 格式。

然而，针对OpenVINO™ 2021.4 LTS以及之前的版本，如果想要直接读取PaddleOCR模型，还需要经过本文第三部分中介绍的若干步骤。

## 2.Paddle OCR特性

- 超轻量级中文 OCR ，总模型仅 8.6M
    - 单模型支持中英文数字组合识别、竖排文本识别、长文本识别
    - 检测模型 DB（4.1M）+识别模型 CRNN（4.5M）
- 多种文本检测训练算法，EAST、DB
- 多种文本识别训练算法，Rosetta、CRNN、STAR-Net、RARE

# 二环境准备

## 1.机器配置

CPU：Intel® Core™ i5-8259U CPU @ 2.30GHz × 8 

OS：Ubuntu 20.04.4 LTS

python：3.8

## 2.下载项目

OpenVINO 对 paddlepaddle 的原生支持计划于OpenVINO 2022.1 版本发布。跟之前对其它框架的支持不同的是，开发者可以直接将 PaddlePaddle 的模型传递给 OpenVINO 直接进行推理，而无需先用 ModelOptimizer 转换成 IR。目前由于 OpenVINOTM 2022.1 版本还没有正式发布，如果您手边安装的是 OpenVINO™ 2021.4 以及之前的版本，那么还需要 clone 以下仓库的代码，并按照后面第三部分中介绍的内容进行环境编译，就能够直接读取 paddleOCR 的模型了。

```
git clone https://github.com/raymondlo84/openvino-paddlepaddle-demo.git
```

![20220227001011](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227001011.png)

# 三.编译与运行

下面是源码编译安装 OpenVINO 的操作步骤

## 1.从 GitHub 克隆 OpenVINO 及其子项目

```
git clone  https://github.com/openvinotoolkit/openvino.git
cd openvino
git submodule update --init --recursive
```

![20220227002035](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227002035.png)

## 2.如果 GitHub 的网络遇到问题，可以切到 gitee 源

```
chmod +x scripts/submodule_update_with_gitee.sh
./scripts/submodule_update_with_gitee.sh
```

![20220227124513](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227124513.png)

## 3.安装 OpenVINO 源码编译依赖

```
chmod +x install_build_dependencies.sh
./install_build_dependencies.sh
pip3 install -r src/bindings/python/src/compatibility/openvino/requirements-dev.txt
```

![20220227120803](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227120803.png)

## 4.编译安装，这儿可能需要花费些时间

```
ls

export OPENVINO_BASEDIR=`pwd`

mkdir build

cd build

cmake  -DCMAKE_BUILD_TYPE=Release -DENABLE_PYTHON=ON -DNGRAPH_PYTHON_BUILD_ENABLE=ON -DPYTHON_EXECUTABLE=`which python3.8` -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.8.so -DPYTHON_INCLUDE_DIR=/usr/include/python3.8 -DENABLE_MKL_DNN=ON  -DENABLE_CLDNN=ON -DNGRAPH_ONNX_IMPORT_ENABLE=ON \-DNGRAPH_DEBUG_ENABLE=ON   ..

make --jobs=$(nproc --all)
```

![20220227151919](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227151919.png)

编译完成

![20220227160256](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227160256.png)

## 5.使用虚拟环境并安装依赖

激活 `OpenVINO_env` 虚拟环境


```
sudo apt install python3.8-venv

cd openvino-paddlepaddle-demo

python3 -m venv openvino_env

source openvino_env/bin/activate

```
![20220227160656](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227160656.png)

安装依赖

```
python -m pip install --upgrade pip

pip install -r requirements.txt -i https://mirror.baidu.com/pypi/simple

```

![20220227161030](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227161030.png)

安装 kernel 到 Jupyter

```
python -m ipykernel install --user --name openvino_env
```

![20220227161303](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227161303.png)

## 6.设置 PaddleDetection

我们将通过 PaddlePaddle 模型库 Paddle Detection 导出 PPYolo 的静态图模型。首先 clone 仓库到项目根目录

```
git clone https://github.com/PaddlePaddle/PaddleDetection.git
```

![20220227161522](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227161522.png)

接下来安装 PaddleDetection 的依赖

```
cd PaddleDetection && git checkout release/2.1 && cd - 

pip install paddlepaddle -i https://mirror.baidu.com/pypi/simple

pip install --upgrade -r PaddleDetection/requirements.txt -i https://mirror.baidu.com/pypi/simple
```

![20220227161750](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227161750.png)

## 7.启动 Jupyter Notebooks

激活 python 虚拟环境，配置 OpenVINO 环境变量，启动 jupyter lab。注意，每一次新的会话，如终端关闭或重启都需要该步骤。

```
source openvino_env/bin/activate

source openvino/openvino_dist/bin/setupvars.sh

jupyter lab notebooks
```

![20220227162414](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227162414.png)

## 8.加载项目

由于这个项目没有推理模型，只有推理脚本。所以这里我们需要下载 PP-OCR 预训练模型

![20220227212144](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227212144.png)

## 9.下载模型

这是我在 Github 找到的 PP-OCR 系列模型列表

[https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.4/doc/doc_ch/models_list.md](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.4/doc/doc_ch/models_list.md)

我们在页面找到 `ch_PP-OCRv2_rec_slim Slim` 量化版超轻量模型，支持中英文、数字识别，点击下载

![20220227205219](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227205219.png)

下载完成后解压

```
tar xf ch_PP-OCRv2_rec_slim_quant_infer.tar
```
![20220227212938](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227212938.png)

进入目录，查看模型信息

![20220227213041](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227213041.png)

把模型导入推理代码中，这里我们修改成刚下载的模型文件的路径

![20220227213231](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227213231.png)

## 10.运行推理

运行推理脚本，看到 print 打印出的信息和原图片信息是一致的，该模型的识别率精确度是很不错的

![20220227213444](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220227213444.png)

# 四.总结

随着我们 OpenVINO 2022.1 版本的推出，以上步骤2-4中这些较为复杂的涉及到源码编译的步骤将可以被省略掉。 只需将第二步中的git命令更换为 

```
git clone --depth=1 https://github.com/OpenVINOtoolkit/OpenVINO_notebooks.git
```

执行第5以及7-10步，就可以得到推理的结果了。具体的安装步骤可以参考这个 GitHub 地址

[https://github.com/OpenVINOtoolkit/OpenVINO_notebooks](https://github.com/OpenVINOtoolkit/OpenVINO_notebooks)




