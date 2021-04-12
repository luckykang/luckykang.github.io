---
layout: post
title: OpenVINO系列24_ubuntu18.04上使用Intel HDDL的demo演示
tag: OpenVINO
---

### 1.安装依赖项

- 找到HDDL_INSTALL_DIR
```
HDDL_INSTALL_DIR 为 <openvino_install_dir>/deployment_tools/inference_engine/external/hddl

cd  /opt/intel/openvino/deployment_tools/inference_engine/external/hddl
```
该目录有以下子文件：

![20210412134027](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412134027.png)

- 执行VPU脚本
```
./install_IVAD_VPU_dependencies.sh
```
- 安装成功，重启
![20210412134103](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412134103.png)

- 然后要重启一下系统

#### 注意：如果更新了linux内核，需要重新安装驱动程序
```
cd /opt/intel/openvino/inference_engine/external/hddl/drivers

sudo ./setup.sh install
```
安装完成后显示

![20210412134226](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412134226.png)

2.开启服务和运行验证脚本

- 开启服务
```
cd /opt/intel/openvino/inference_engine/external/hddl/bin 
./hddldeamon
```

执行完毕后，如果出现 SERVICE IS READY 说明服务已经启动

![20210412134308](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412134308.png)

- 使用HDDL插件运行验证脚本
```
cd /opt/intel/openvino/deployment_tools/demo

./demo_squeezenet_download_convert_run.sh -d HDDL
```

