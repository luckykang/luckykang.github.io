---
layout: post
title: 机器学习系列07-使用Azure Designer 训练模型
tag: 机器学习
---

### 1.Azure平台介绍

Azure Machine Learning(简称'AML')是微软在Azure上推出的基于Web使用的一项机器学习服务，这款服务的目的是在“将机器学习与云计算的简单性相结合”，简化使用机器学习的过程，便于开发人员、业务分析师和数据科学家进行广泛、便捷的应用。AML中提供了三种训练模式，分别为Notebooks、Automated ML、Designer，今天我们说一下怎么使用Designer。


### 2.使用前准备

国际版功能比较全面，建议使用国际版。链接： [https://portal.azure.com](https://portal.azure.com)

注册登录账号以后首先创建资源

![20210816221226](https://i.loli.net/2021/08/16/jI9e6Ad2ValhkHf.png)

点击`Machine Learning`创建。填好相应的资源组信息和工作区信息，`Region`建议选择 "East Asia",网速会快一些，然后点击按钮。

![20210816221614](https://i.loli.net/2021/08/16/1p9csF4alywUMSJ.png)

看到信息已经生成，点击创建。

![20210816221952](https://i.loli.net/2021/08/16/ohtGPXfYqOVuDQa.png)

接下来需要等待服务器完成，大概一两分钟，具体取决于网络环境。完成后，页面会刷新。

![20210816222216](https://i.loli.net/2021/08/16/crUPt9KRYXIl1o2.png)

点击`Launch studio`进入工作区

![20210816223142](https://i.loli.net/2021/08/16/WPtvQCeIx81BJES.png)

到这里，我们就找到了文章要介绍的主角 Designer 了。

### 3.配置Designer的pipeline

点击`Start now`,跳转页面后创建`New pipeline
`

![20210816224229](https://i.loli.net/2021/08/16/mUKDW6xbLQjfFzt.png)

`pipeline`创建成功

![20210816224428](https://i.loli.net/2021/08/16/DmFVzyuQkEN7irc.png)

### 4.使用Azure Designer进行模型训练

#### 4.1导入本地数据集

点击`Datasets`， 选择从本地导入

![20210816224929](https://i.loli.net/2021/08/16/9VMNcGp3KtxJeRA.png)

填写基础信息，因为我要导入csv格式的数据集，所以`Dataset type`使用'Tabular'

![20210816225357](https://i.loli.net/2021/08/16/OQK7PhbrfHcRaY2.png)

点击`upload`上传数据集。

![20210816230111](https://i.loli.net/2021/08/16/ipqyn18k2dEBP3l.png)

显示数据集的细节信息

![20210816230956](https://i.loli.net/2021/08/16/A3ujwPirIXg89Uc.png)

提示成功后再次点击`Datasets`按钮，就可以看到新创建的数据集啦

![20210816231214](https://i.loli.net/2021/08/16/ABMm1ke7tHzdClE.png)

#### 4.2加载、分割数据集

点击`Experiments`，创建pipeline

![20210816234108](https://i.loli.net/2021/08/16/cmPFLbt8drzWsKT.png)

使用已上传的数据集

![20210817000742](https://i.loli.net/2021/08/17/ObdZTUP6lVt2Hog.png)

加载完成

![20210817001216](https://i.loli.net/2021/08/17/UFTuHjzVSYCBpyP.png)

分割数据集。在`Data Transformation`中找到`Split Data`

![20210817001502](https://i.loli.net/2021/08/17/8qzHE2ZSTMrtJYh.png)

连接`Split Data`和`train`数据集

![20210817001754](https://i.loli.net/2021/08/17/t2QlZDrORifw7qN.png)

点击分割模块，并设置`Fraction of rows in the first output dataset`为0.8，将80%的数据用于训练模型，剩余的20%数据验证模型的精度。

![20210817002221](https://i.loli.net/2021/08/17/jRPmZfLYbNuSU3c.png)

#### 4.3进行模型训练

展开`Model Training` 下的`Train Model`，拖拽到设计器中，最后在设计器连接`Train Model`和`Split Data`

![20210817103100](https://i.loli.net/2021/08/17/Z6z52eTE3vmaUJw.png)

借助Azure机器学习算法评估模型的数据。在左窗口展开`Machine Learning Algorithms`即机器学习模块， 在子模块`Classification `中选择算法，拖拽到设计器中，并连接`Train Model`。

![20210817205339](https://i.loli.net/2021/08/17/kNSHycfV6EPDMA8.png)

我们需要指定数据集中的一列数据作为输出或者预测列（即选择预测项），这一列将基于其他列的数据做预测，来完成算法的配置。此时看到`Train Model`还有红色三角形，表示还没配置完成。点击该模块，右侧选择`Label column`,这里我选择用户的收入作为预测列。

![20210817212816](https://i.loli.net/2021/08/17/IksFLQd5B27RrD3.png)

