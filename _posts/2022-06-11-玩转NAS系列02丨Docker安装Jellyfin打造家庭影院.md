---
layout: post
title: 2022-06-11-玩转NAS系列01丨Docker安装Jellyfin打造家庭影院
tag: NAS
---

# 一.群晖docker套件加速下载

使用docker界面版下载应用的时候，经常会遇到下载中断或者失败，这是因为默认的docker源的问题。

## 解决方法1：在注册表的设置中添加国内源,点击编辑。

![20220611085020](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611085020.png)

可以在红框填入镜像加速url。

![20220611092902](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611092902.png)

## 解决方法2：使用阿里云镜像加速器，点击下面地址，登录就可以获取自己的专用加速。

[https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

ssh登录nas，然后根据命令在json配置文件中添加好加速源。

![20220611085532](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611085532.png)

在终端直接执行`sudo docker pull jellyfin/jellyfin` 就可以拉取到最新的image,几百MB的文件不到一分钟就可以下载好，太爽了！

![20220611085754](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611085754.png)

补充一点是这里我ssh登录的时候，遇到了一个报错，这是因为在我的clint端有之前配置的错误的key,导致可以ping通，但是登录失败。

![20220611090127](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611090127.png)

解决办法是`ssh-keygen -R 192.168.2.124`，重新在client生成一个key,再次登录就好了。

![20220611090454](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611090454.png)


# 二.Jellyfin的docker配置

## 1.安装完成后，就可以在映像中看到jellyfin了

![20220611091105](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611091105.png)

## 2.在docker文件夹内新建`jellyfin`文件夹

![20220611091419](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611091419.png)

## 3.设置一下这个文件夹的权限，对jellyfin文件夹右键，点属性，然后点权限，设置everyone 全部权限打开

![20220611091732](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611091732.png)

## 4.新建一个文件夹保存电影，这里我设置的是一个共享文件夹，名字为`OWDownload`，并且把电影都放入了这个文件夹

![20220611104936](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611104936.png)

## 5.双击打开下载好的镜像，点击`高级设置`-`存储空间`-`添加文件夹`,设置装载路径

![20220611105243](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611105243.png)

## 6.设置一下端口，默认就是8096

![20220611105417](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611105417.png)

## 7.设置完成后`应用`并点击`下一步`,后点击左下角的向导完成后运行此容器

![20220611105551](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611105551.png)

## 8.此时看到已在运行中

![20220611105658](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611105658.png)

# 三. 设置与使用jellyfin

## 1.在浏览器中输入`http://192.168.2.124:8096/`，进入到了jellyfin设置页面，默认是英文，先设置成中文

![20220611105908](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611105908.png)

## 2.填写用户和密码

![20220611110221](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611110221.png)

## 3.设置媒体库，先跳过

![20220611110453](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611110453.png)

## 4.选择语言

![20220611110706](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611110706.png)

## 5.配置远程访问

![20220611110747](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611110747.png)

## 6.点击完成，然后登录,点击`控制台`

![20220611111124](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611111124.png)

## 7.点击`媒体库`，添加媒体库

![20220611111254](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611111254.png)

## 8.选择内容类型和语言

![20220611111455](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611111455.png)

## 9.然后点击文件夹，找到`/media`并选择

![20220611111839](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611111839.png)

![20220611112034](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611112034.png)

## 10.保存媒体图像到文件夹打钩

![20220611112103](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611112103.png)

## 11.点击确定后就开始自动扫描媒体库了，这个过程，会自动根据电影的文件名在网上进行刮削，制作电影墙。完成后点击home按键就可以在看到电影信息了。

![20220611133512](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611133512.png)

## 12.安装字幕插件

![20220611113111](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611113111.png)

安装后提示重启jellyfin，在docker里点击重启。

![20220611113301](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20220611113301.png)

