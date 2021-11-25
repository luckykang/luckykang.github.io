---
layout: post
title: TDengine系列02丨TDengine物理节点的集群搭建
tag: TDengine
---

## 一.前言

出于对技术选型的验证评估，我们想搭建一个容器化的Tdengine集群。但是官方文档说不建议在生产环境中部署容器化集群。问了下技术人员，回复说容器化的部署会损失部分性能，具体损失百分之多少，由于docker资源是灵活分配的，不好做标准评估。所以先搭建一个物理集群，熟悉一下集群配置的具体流程,为搭建容器化的集群应用做准备。

## 二.集群相关概念说明

### 1.集群的数据节点

是由End Point来唯一标识的，End Point是由FQDN(Fully Qualified Domain Name)外加Port组成，比如 h1.taosdata.com:6030

### 2.什么是FQDN

FQDN(fully qualified domain name,完全限定域名)是internet上特定计算机或主机的完整域名。FQDN由两部分组成:主机名和域名。例如，假设邮件服务器的FQDN可能是mail.taosdata.com。主机名是mail，主机位于域名taosdata.com中。

DNS(Domain Name System)，负责将FQDN翻译成IP，是互联网绝大多数应用的寻址方式。

**加入到集群中的数据节点dnode，涉及集群相关的下表11项参数必须完全相同**

![20211124180612](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124180612.png)

## 三.集群架构图

这里我用两台机器做数据的采集和存储，用`FQDN`(Fully Qualified Domain Name，完全限定域名)来进行节点之间的通信。应用端无需配置集群节点IP/FQDN地址，而仅配置`firstEP`实现集群寻址。

![20211124175306](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124175306.png)


![20211124234932](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124234932.png)

## 四.运行环境

在h1、h2节点部署了telegref、tdengine采集存储数据，在cli端部署了tdengine的客户端程序和grafana，便于数据的展示。

![20211124182233](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124182233.png)

## 五.搭建过程

### 1.检查环境

- 清理环境

如果搭建集群的物理节点中，存有之前的测试数据、装过1.X的版本，或者装过其他版本的TDengine，请先将其删除，并清空所有数据（`rm -rf /var/lib/taos/*`）。

- 关闭防火墙，开启对应的端口

关闭所有物理节点的防火墙，至少保证端口：`6030 - 6042`的TCP和UDP端口都是开放的

- 服务器的时钟同步

Ubuntu的默认安装现在使用timesyncd而不是ntpd。各节点终端输入`timedatectl`将打印出本地时间，通用时间（如果您没有从UTC时区切换，可能与本地时间相同），以及一些网络时间状态信息。要确保每个节点都开启同步。

`System clock synchronized`: 
yes表示时间已成功同步，

`systemd-timesyncd.service active`: yes表示已启用并运行timesyncd

![20211124183611](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124183611.png)

### 2.配置节点的FQDN解析

规划集群所有物理节点的FQDN，将规划好的FQDN分别添加到每个物理节点的`/etc/hostname`；修改每个物理节点的`/etc/hosts`，将所有集群物理节点的IP与FQDN的对应添加好。

配置h1节点

![20211124181452](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124181452.png)

配置h2节点

![20211124181338](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124181338.png)

**重启系统**

在h1节点ping h2节点，看到fqdn解析成功。

![20211124182444](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124182444.png)

在h2节点ping h1节点，解析成功。

![20211124182621](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124182621.png)

在**客户端**，首先配置hosts，在以下路径打开hosts文件

![20211124225404](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124225404.png)

添加h1、h2节点的fqdn解析

![20211124225441](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124225441.png)

配置好以后，在终端ping h1、h2，解析成功

![20211124225754](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124225754.png)

### 3.安装TDengine和telefgraf

在h1、h2两个节点安装TDengine Server，且版本必须是一致的

![20211124183938](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124183938.png)

注意安装完成后**先不要启动服务**

![20211124184010](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124184010.png)

然后安装telegraf并修改`/etc/telefraf/telegraf.conf`,2.3版本的url形式按下图设置。
![20211124185124](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124185124.png)

**注意要注释[[outputs.influxdb]],不然无法写入TDengine**,telegraf会显示如下报错

![20211125002437](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002437.png)

### 4.TDengine的配置

在所有的数据节点，firstEp需全部配置成一样的，但fqdn配置为每个数据节点本地的值。firstEp 是每个数据节点首次启动后连接的第一个数据节点。

配置h1节点

![20211124184719](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124184719.png)

在taos.cfg中配置h1的参数，这里我们让h1作为第一个启动节点，所以firstEP配置为自己的。

![20211124184801](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124184801.png)

配置h2的参数，firstEP要设置为h1的fqdn

![20211124184946](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124184946.png)

**客户端**安装完成TDengine cli后，在安装路径下`/cfg`目录下，打开taos.cfg文件

![20211124223140](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124223140.png)

客户端是不需要配置fqdn参数的，只需配置firstEP,设置为h1节点。

![20211124223347](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124223347.png)

使用curl命令访问h1节点，可以看到有数据返回，说明配置成功，也可以通过ping的方式。

    curl -H "Authorization: Basic cm9vdDp0YW9zZGF0YQ==" -X POST -d "show databases"   "http://h1.taosdata.com:6041/rest/sqlutc"

![20211124180037](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124180037.png)

## 六.管理节点

在TDengine cli登录h1节点，查看集群所有的dnodes

![20211124231119](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211124231119.png)

查看vgroups

![20211125002216](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002216.png)

查看mnodes和dnodes

![20211125002306](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002306.png)

创建新不存在的节点h7

![20211125001705](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125001705.png)

删除h7节点，因为节点并不真实存在，所以状态是0ffline

![20211125001810](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125001810.png)

建库udb

![20211125001958](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125001958.png)

建表tb_1

![20211125002112](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002112.png)

表中插入数据

![20211125002344](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002344.png)

## 七.配置grafana

### 1.下载

下载grafana和tdengine-datasource

### 2.添加plugin

配置tdengine-datasource，把下载的插件目录放到以下路径

![20211125002840](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002840.png)

### 3.修改配置

该路径下复制sample.ini文件，并重命名为custom.ini

![20211125003358](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003358.png)

添加代码使之生效

![20211125003609](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003609.png)

运行grafana服务

![20211125002652](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002652.png)

添加TDengine数据库

![20211125002802](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125002802.png)

配置host和相关参数，点击保存

![20211125003707](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003707.png)

## 八.在grafana中展示数据

可以在仪表盘中查看两个节点的数据

![20211125003827](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003827.png)

h1节点的数据展示

![20211125003859](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003859.png)
![20211125003915](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003915.png)
![20211125003932](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125003932.png)

h2节点的数据展示

![20211125004105](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125004105.png)
![20211125004116](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125004116.png)
![20211125004126](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125004126.png)


## Q&A

![20211125082831](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211125082831.png)