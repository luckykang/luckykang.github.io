---
layout: post
title: VS Code系列04丨Windows配置C语言的开发环境
tag: VS Code
---

### 一.下载安装MinGW编译器

C/C++的编译器有很多种，这里选择开源的MinGW编译器,浏览器搜索下载。下载完成后点击`mingw-get-setup.exe`,等待安装完成

![20210922161150](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922161150.png)

完成后点击`continue`,在刷新的页面勾选`gcc-g++`,

![20210922161410](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922161410.png)

然后点击左侧的`All Packages`,按下图进行勾选

![20210922161652](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922161652.png)

然后点击`Apply Changes`,

![20210922161739](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922161739.png)

弹出页面中点击`Apply`

![20210922161859](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922161859.png)

这一步的等待时间会比较长

![20210922161945](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922161945.png)

到这一步就安装完了，点击关闭

![20210922162136](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922162136.png)

### 二.检查安装情况并配置环境变量

打开安装路径，查看`g++` `gcc` `gdb`,如果有就安装好了

![20210922162305](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922162305.png)
![20210922162414](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922162414.png)

打开系统环境变量

![20210922162746](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922162746.png)

添加环境变量

![20210922162837](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922162837.png)

在终端输入`gcc -v`看到安装完成

![20210922163131](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922163131.png)

### 三.在VS Code中配置

#### 1.安装C/C++扩展

![20210922165206](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922165206.png)

接下来配置编译器路径，按快捷键`Ctrl+Shift+P`调出命令面板，输入`C/C++`，选择`Edit Configurations(UI)`进入配置,设置编译器路径。

![20210923105317](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923105317.png)

#### 2.安装配置code-runner

![20210923095622](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923095622.png)

右键点击`Extension Settings`

![20210923095744](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923095744.png)

配置code-runner扩展功能

![20210922171035](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922171035.png)
![20210923101707](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923101707.png)

然后就可以不用终端输入，可以直接点击运行代码啦

![20210923100213](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923100213.png)

### Q&A

1.在vscode运行代码的时候报错`无法加载文件 WindowsPowerShell\profile.ps1,因为在此系统上禁止 运行脚本`

解决办法：win+x键，打开powershell,运行下面代码，即可解决。

    set-ExecutionPolicy RemoteSigned   

