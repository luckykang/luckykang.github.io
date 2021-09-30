---
layout: post
title: VS Code系列04丨Windows配置C语言的开发环境
tag: VS Code
---

### 一.下载安装MinGW编译器

C/C++的编译器有很多种，这里选择开源的MinGW编译器,浏览器搜索下载。点击[https://sourceforge.net/projects/mingw-w64/](https://sourceforge.net/projects/mingw-w64/),下拉找到在线安装方式，下载exe文件。

![20210926172432](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210926172432.png)

双击打开`mingw-w64-install.exe`，如下图所示

![20210926172544](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210926172544.png)

点击下一步，选择安装信息

![2021-09-26-17-34-29](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-09-26-17-34-29.png)

*安装信息说明*

- Version: 指的是 gcc 的版本，如果没有特殊的需求，一般选择最高的版本号即可。最高版本是8.1.0 ，选中它即可

- Architechture:电脑系统是64位的，选择x86_64；如果是32位系统，则选择i686

- Threads:如果是 Windows ，选择 win32 ，如果是 Linux、Unix、Mac OS 等其他操作系统要选择 posix

- Exception：seh 是新发明的，而 sjlj 则是古老的。seh 性能比较好，但不支持 32位。 sjlj 稳定性好，支持 32位。

最终选择如下

![2021-09-26_17-37-48](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-09-26_17-37-48.png)

选择完成后点击下一步，配置安装路径

![2021-09-26_17-39-00](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-09-26_17-39-00.png)

到这一步就安装成功了

![2021-09-26_17-41-18](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-09-26_17-41-18.png)

### 二.检查安装情况并配置环境变量

打开安装路径，查看`g++` `gcc` `gdb`,如果有就安装好了

![20210926213851](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210926213851.png)

打开系统环境变量

![20210922162746](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922162746.png)

复制安装路径

![2021-09-26_17-44-15](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-09-26_17-44-15.png)

添加path环境变量

![2021-09-26_17-45-45](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-09-26_17-45-45.png)

在终端输入`gcc -v`显示已安装的gcc版本信息

![20210926214222](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210926214222.png)

### 三.在VS Code中配置

#### 1.安装C/C++扩展

![20210922165206](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922165206.png)

接下来配置编译器路径，按快捷键`Ctrl+Shift+P`调出命令面板，输入`C/C++`，选择`Edit Configurations(UI)`进入配置,设置编译器路径。

![20210927100342](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210927100342.png)

#### 2.安装配置code-runner

![20210923095622](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923095622.png)

右键点击`Extension Settings`

![20210923095744](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923095744.png)

配置code-runner扩展功能，勾选下图四项

![20210922171035](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210922171035.png)
![20210923101707](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923101707.png)

然后就可以不用终端输入，可以直接点击运行代码啦

![20210923100213](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210923100213.png)

### Q&A

1.在vscode运行代码的时候报错`无法加载文件 WindowsPowerShell\profile.ps1,因为在此系统上禁止 运行脚本`

解决办法：win+x键，打开powershell,运行下面代码，即可解决。

    set-ExecutionPolicy RemoteSigned   

