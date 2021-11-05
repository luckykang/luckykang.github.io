---
layout: post
title: ARM NanoPi丨旁路由怎么玩
tag: Tools
---

本文配置的是非侵入式的旁路由的设置，这样的好处是不影响既有的网络结构，可以扩展现有的功能，丰富多个终端的使用。简单说就是，想用主路由的就用主路由，想用旁路由的设备就可以使用旁路由，从而实现不同的功能。

### 1.设置旁路由和主路由在一个网段

#### 1.查看主路由的ip

进主路由后台查看局域网ip,我这里是小米默认是192.168.31.1

#### 2.修改旁路由ip，使与主路由在同一网段

旁路由的lan口接到电脑，然后登录192.168.2.1进入后台，修改ip为192.168.31.xx,后边的数字可以在2-254之间自由填写。这里选择为192.168.31.2。点击保存后，把旁路由的lan口接到主路由的lan口。

![20211106005405](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106005405.png)

### 2.设置旁路由的网关指向主路由

电脑用wifi连接到主路由后，再次登录旁路由192.168.31.2。按下图进行设置。

![20211106010206](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106010206.png)

![20211106010320](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106010320.png)

![20211106010356](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106010356.png)

### 3.防火墙开启IP动态伪装

在网络-防火墙的配置，见下图

![20211106010552](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106010552.png)

### 4.终端如何使用旁路由访问网络

电脑，手机等想使用旁路由，需要各自配置。主要是TCP/IP和DNS的配置。如下图

需要注意的是，路由器（MAC这样显示的，windows应该是网关）要指向旁路由的IP。

![20211106011010](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106011010.png)

DNS指向主路由就可以

![20211106011106](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211106011106.png)

好啦，这样就可以愉快的上网啦！即使旁路由挂了，也不影响主路由的网络访问，达到了解耦。