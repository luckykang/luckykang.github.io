---
layout: post
title: vs code配置python环境
tag: 工具
---



### 1.安装中文汉化包

Chinese (Simplified) Language Pack 

### 2.安装 python

完成后左下角会出现你的电脑上安装的 Python 版本

### 3.安装 Material Icon Theme   

这是一个比默认更好看的图标样式

### 4.设置代码自动保存

在菜单栏的文件，勾选自动保存即可

### 5.安装 Project Manager

工作中，我们经常会来回切换多个项目，每次都要找到对应项目的目录再打开，比较麻烦。Project Manager插件可以解决这样的烦恼，它提供了专门的视图来展示你的项目，我们可以把常用的项目保存在这里，需要时一键切换，十分方便。

### 6.安装 Code Time

记录编程时间，统计代码行数。安装该插件后，登陆github账户后,VS Code 底部的状态栏右下角可以看到时间统计。在Project Manager界面点击 view summary可以查看代码编写时间。

### 7.安装 indent-rainbow  

突出显示缩进

### 8.安装 yapf   

用来格式化代码

执行：

    pip install yapf 

打开vs code的设置，搜索yapf,然后在用户扩展中设置为yapf,然后在代码中点击鼠标右键，选择格式化代码，就会对代码进行格式化的操作

### 9.安装主题  One Dark Pro

这个主题很清新护眼,我很喜欢

### 10.安装字体 Fira Code

下载地址为：[https://github.com/tonsky/FiraCode](https://github.com/tonsky/FiraCode)

安装方法：

解压后进入ttf文件夹，选中所有文件拷贝到C:\Windows\Fonts目录中，在vs code的设置中settings.json中添加下列代码，重启vs code就会使用该字体：

    //更改字体为Fira Code
    "editor.fontFamily": "Fira Code",
    "editor.fontLigatures": true,
    
<br>

转载请注明：[康瑶明的博客](https://luckykang.github.io) 







