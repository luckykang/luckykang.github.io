---
layout: post
title: Tools系列06-Ubuntu录制gif
tag: Tools
---

### 1.工具: kazam和ffmpeg

kazam是linux下的一款简单但是功能强大的屏幕录制工具. 它可录制声音并选择全屏录制或局部区域录制.

    sudo add-apt-repository ppa:kazam-team/unstable-series
    sudo apt-get update && sudo apt-get install kazam

若安装成功, 在终端输入以下指令可打开该软件界面

    kazam 

FFmpeg是用于录制、转换和流化音频和视频的完整解决方案， 包括 libavcodec ，一套领先的音/视频编解码类库。FFmpeg 在Linux上开发，当可以在大多数操作系统下编译，包括Windows。

在ubuntu上, 在终端输入以下命令:

    sudo apt-repository ppa:kirillshkrogalev/ffmpeg-next 
    sudo apt-get update
    sudo apt-get install ffmpeg

 安装完成后, 在终端输入

    ffmpeg -version

若出现版本信息, 说明安装成功.

### 2.Gif制作
#### 1.视频录制

打开Kazam软件. 其中Fullscreen 表示录制整个平面, Window表示录制特定的窗口, Area表示自己选取某个区域. 这个可根据自己需求自行选择.

![20210407172647](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210407172647.png)

选择好录制区域后, 点击Capture按钮, 此时屏幕开始倒计时录制. 倒计时时间可自己设置. 录制完成的视频保存为`test.mp4`文件.

#### 2.将视频转化为Gif

ffmpeg功能强大, 这里只是介绍将视频转为Gif时常用的指令，要将之前保存的test.mp4文件转化为Gif, 可简单的使用下面命令:

    ffmpeg -i test.mp4 out.gif

这是按照默认的参数。其中test.mp4是输入的视频文件, out.gif 为输出文件. 但是问题来了, 此时的源视频可能为1080P的高清视频, 转化后的Gif文件很可能比源视频都大的多. 所以为了达到自己的要求, 需要添加相应的参数. 通常来讲, 下面的对下面指令的参数进行适当修改一般都可满足我们的要求.

    ffmpeg -ss 2 -t 12 -i test.mp4 -s 649x320 -r 15 output1.gif
其中,  `-ss 2 to 12` 表示从从视频的第2秒开始转换, 转换时间长度为12秒后停止. `-s`用于设定分辨率, `-r`用于设定帧数。通常Gif有15帧左右就比较流畅了。

![975974-20161006155926739-207209539](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/975974-20161006155926739-207209539.gif)

