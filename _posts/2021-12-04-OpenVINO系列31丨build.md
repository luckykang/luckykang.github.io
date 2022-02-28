---
layout: post
title: OpenVINO系列31丨build
tag: OpenVINO
---

# clone代码

从GitHub克隆OpenVINO及其子项目

```
git clone  https://github.com/openvinotoolkit/openvino.git

cd openvino

git submodule update --init --recursive
```
# 切换gitee源

```
chmod +x scripts/submodule_update_with_gitee.sh

./scripts/submodule_update_with_gitee.sh
```

# 安装OpenVINO源码编译依赖

```
chmod +x install_build_dependencies.sh

./install_build_dependencies.sh

pip3 install -r src/bindings/python/src/compatibility/openvino/requirements.txt
```

# build

```
mkdir build 

cd build

cmake  -DCMAKE_BUILD_TYPE=Release -DENABLE_PYTHON=ON -DNGRAPH_PYTHON_BUILD_ENABLE=ON -DPYTHON_EXECUTABLE=`which python3.8` -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.8.so -DPYTHON_INCLUDE_DIR=/usr/include/python3.8 -DENABLE_MKL_DNN=ON  -DENABLE_CLDNN=ON -DNGRAPH_ONNX_IMPORT_ENABLE=ON -DNGRAPH_DEBUG_ENABLE=ON   ..

make --jobs=$(nproc --all)

cmake -DNGRAPH_PDPD_FRONTEND_ENABLE=ON -DENABLE_SAMPLES=OFF -DENABLE_CLDNN=OFF -DENABLE_PYTHON=ON -DNGRAPH_PYTHON_BUILD_ENABLE=ON -DENABLE_MYRIAD=OFF -DNGRAPH_ONNX_IMPORT_ENABLE=ON -DCMAKE_INSTALL_PREFIX=`pwd`/install -DNGRAPH_UNIT_TEST_ENABLE=ON -DNGRAPH_USE_SYSTEM_PROTOBUF=OFF -DPYTHON_EXECUTABLE=$(which python3) -DCMAKE_EXPORT_COMPILE_COMMANDS=ON -DENABLE_GNA=OFF -DENABLE_CLANG_FORMAT=ON -DENABLE_TESTS=ON -DENABLE_FUNCTIONAL_TESTS=ON -DENABLE_STRICT_DEPENDENCIES=OFF \
-DENABLE_FASTER_BUILD=ON \
-DDNNL_LIBRARY_TYPE=SHARED \
..

```
# initialized

```
cd scripts/setupvars/

mkdir python

cp -rf  ~/openvino/bin/intel64/Release/lib/python_api/python3.8/    ./python

source ~/openvino/scripts/setupvars/setupvars.sh
```

![20220228230659](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220228230659.png)

![20220228230609](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220228230609.png)