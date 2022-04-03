---
layout: post
title: Tools系列11丨Windows最好用的ssh终端利器
tag: Tools
---

# 一.主流的ssh工具有哪些

Termius:

优点是界面优美，支持全平台，缺点就是需要账户登录，是一款付费软件，免费版不支持SFTP，如果舍得花钱体验很棒。

![20220403172349](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403172349.png)

Putty:

是一款成熟免费的ssh工具，稳定开源无广告，如果只链接远程终端的话，使用很顺手。经常和服务器传输文件就不推荐了。

![20220403172434](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403172434.png)

Mobaxterm:

Mobatek 开发的提供所有重要的远程网络工具（SSH、X11、RDP、VNC、FTP、MOSH...）和Unix 命令（bash、ls、cat、sed、grep、awk、rsync...）的工具, 功能十分强大。分为免费和付费版。唯一的缺点是免费版本有ssh会话有个数限制。

![20220403172732](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403172732.png)

electerm:

是一款在github开源的ssh终端工具，最大的优势是支持github的账号同步，当然也支持SFTP，功能比较强大，不过UI个人不是很喜欢，自带的theme少，也不美观，可完善的地方还有很多。

![20220403172710](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403172710.png)

Winsshterm:

这是我用过市场上主流的ssh tools后坚定选择的，也是我最喜欢的。

它好在哪里呢?

第一就是开源免费，使用自由。

第二是不限制会话个数，支持SFTP功能，方便文件的互传。

第三是使用流畅，界面简洁，交互人性化。

![20220403172516](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403172516.png)

# 二. winsshterm介绍

它是一款适用于 Windows 的选项卡式 SSH 解决方案，它的使用依赖于其他工具的安装。结合了 PuTTY/KitTY（SSH 功能）、WinSCP（文件传输） 和 VcXsrv（支持 X (VcXsrv)，带有复制和粘贴功能）等服务。

# 三.下载

## 1.从Microsoft Store

## 2.从winsshterm官网

[https://winsshterm.blogspot.com/](https://winsshterm.blogspot.com/)

## 3.从github

[https://github.com/WinSSHTerm/WinSSHTerm](https://github.com/WinSSHTerm/WinSSHTerm)

# 四.安装与设置

## 1.安装winsshterm

安装路径

**C:\Users\kangy1x\Documents\WinSSHTerm**

安装完成后会提示安装其他几个软件，建议下载后放在上述路径的`tools`目录下

![20220403180844](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403180844.png)

## 2.配置ssh

在`Terminal`选项中配置Putty的路径，配置完成后显示**file is present**说明配置成功

![20220403181038](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403181038.png)

## 3.配置winscp

在`Copy Files`选项中配置winscp服务

这里要注意的是，如果勾选 `show download dialog on startup if not present`，就**不要勾选**下面的`Use a custom location`选项，否则会导致冲突，安装不成功

![20220403181248](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403181248.png)

## 4.配置VcXsrv服务

VcXsrv是Microsoft Windows的开源显示服务器。它使 Windows操作系统的用户能够运行为X Window 系统设计的GUI程序。

![20220403181706](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403181706.png)

## 5.配置Pageant服务

Pageant是PuTTY身份验证代理。它会将你的私钥保存在内存中，以便您在连接到服务器时可以使用它们。 它消除了以下需求： 如果使用多个帐户登录服务器，则为每个Linux用户帐户明确指定相关密钥。

![20220403181958](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403181958.png)

## 6.配置跳转服务

plink支持SSH跳转服务器/代理。

![20220403182137](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403182137.png)

# 五.如何使用SFTP功能

选中要传输文件的服务器，右键点击`copy Files`

![20220403182657](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403182657.png)


winscp服务启动成功。

![20220403182928](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220403182928.png)