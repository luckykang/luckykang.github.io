---
layout: post
title: 问题集锦001丨ImportError ('You must install pydot (`pip install pydot`) and install graphviz
tag: 问题集锦

---

# ImportError: ('You must install pydot (`pip install pydot`) and install graphviz 怎么解决？

### 1.首先安装 `pip install pydot` 

### 2.登录graphviz官网，下载包

[https://graphviz.gitlab.io/download/](https://graphviz.gitlab.io/download/)

### 3.执行安装

![image-20220111145956566](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/202201111500965.png)

## 4.查看环境变量,并重启

![image-20220111151342127](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/202201111513185.png)

## 5.导入plot_model

[Keras](https://so.csdn.net/so/search?q=Keras)中提供了一个神经网络可视化的函数plot_model，并可以将可视化结果保存在本地。

```python
from tensorflow.python.keras.utils.vis_utils import plot_model
```
