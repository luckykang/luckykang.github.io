---
layout: post
title: OpenVINO系列11_Vtune对human-pose-estimation在CPU推理运行的性能分析
tag: OpenVINO
---

>>> 全文字数766，预计阅读4min

### 一、什么是VTune？

Vtune可视化性能分析器（Intel VTune Performance Analyzer）是一个用于分析和优化程序性能的工具，作为Intel为众多开发者们提供的专门针对寻找软硬件性能瓶颈的一款分析工具，它能帮助你确定程序的热点（hotspot），帮助你找到导致性能不理想的原因，从而让你能据此对程序进行优化。

概括起来Vtune性能分析器能通过以下的手段帮助你发现和定位程序中的性能问题：
- 从当前系统中收集性能数据
- 从系统到源代码不同的层次上，以不同的互动形式来组织和展示数据
- 发现潜在的性能问题，并提出改进措施。

### 二、下载vtune：

VTune的正式版的价格很贵，可以选择下载试用版，这里以2020_update2版本为例，下载地址为：

[https://software.intel.com/en-us/vtune/choose-download](https://software.intel.com/en-us/vtune/choose-download)

选择版本和系统平台，这里以linux系统为例

![Snipaste_2020-08-10_16-38-20](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/Snipaste_2020-08-10_16-38-20.png)

### 三、解压安装

#### 1.解压安装包

![0810164621](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0810164621.png)

#### 2.安装

进入解压目录，执行GUI界面安装，按照提示安装完成即可，sudo权限下默认安装路径为`/opt/intel/`

如下图：

![0810165002](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0810165002.png)

#### 3.检查是否安装成功

进入安装目录`/opt/intel/vtune_profiler/bin64`，执行自检脚本检查是否安装成功。
如下图，该脚本在具有可靠热点的样本上运行多个代表性分析类型。

![0811110027](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0811110027.png)


脚本完成后，它会生成一个日志文件，并对检查的成功或失败进行诊断。下图为部分终端状态的截图，显示状态都是ok的，说明安装成功了。

![0811134256](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0811134256.png)

#### 4.配置VTune Amplifier环境

在终端执行命令 `source  /opt/intel/vtune_profiler/env/vars.sh` 或者在basrhc环境变量中写入该命令。


### 四、新建性能分析工程，配置和运行

#### 1.启动VTune性能分析器

在目录`/opt/intel/vtune_profiler/bin64`执行  `sudo ./vtune-gui` ,启动vtune

![0811170653](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0811170653.png)

#### 2.按图操作，新建性能分析工程

![0811171936](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0811171936.png)

在弹出的对话框中填写项目名，点击创建。可以看到项目所在目录为`/home/kang/intel/vtune/projects`

![0811172118](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0811172118.png)

#### 3.填写项目路径和参数

在Launch Application一栏填写要分析的目标文件的路径，这里我们以human_pose_estimation_demo为例演示

![0814113024](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814113024.png)

找到该文件路径，选中文件并打开，vtune就会把路径自动填充到框内。

![0814113637](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814113637.png)

在Application parameters一栏填写要分析的文件的执行参数

![0814134258](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814134258.png)

打开右面界面，可以看到CPU的默认采样时间为10ms

![0814152631](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814152631.png)

### 4.运行分析，收集数据

点击开始分析数据后，VTune就开始运行目标程序，并收集相关的性能数据

![0814134816](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814134816.png)

### 五、性能分析

停止数据分析后，可以获得几类数据，分别为“Collection Log”、“Summary”、“Bottom-up”、“Caller/Callee”、“Top-down Tree”和“Platform”。

这里我们主要说一下Summary。Summary主要分析的数据有：“Elapsed Time（经过的总时间）”、“Effective CPU Utilization Histogram （有效的CPU利用率直方图）”和“Collection and Platform Info（收集信息和平台信息）”。

![0814154357](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814154357.png)

Elapsed Time信息，主要有 Effective Time (有效时间)、
Total Thread Count（总线程数量）、 Paused Time (暂停时间)。

![0814154553](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814154553.png)

Effective CPU Utilization Histogram信息，显示了CPU使用直方图

![0814164225](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814164225.png)

Collection and Platform Info信息，包含了应用程序命令行、操作系统、CPU等信息。

![0814164123](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0814164123.png)



<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)
