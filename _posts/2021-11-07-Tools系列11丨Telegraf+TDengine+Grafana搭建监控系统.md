---
layout: post
title: Tools系列11丨Telegraf+TDengine+Grafana搭建监控系统
tag: Tools
---


### 一.常用的监控系统方案有哪些？各自有什么优缺点？

#### 1.常见的系统服务监控方案有下面几种：

方案1：Exporter + prometheus + Grafana

方案2：Telegraf + prometheus + Grafana

方案3：Telegraf + TDengine + Grafana

方案4：Telegraf + InfluxDB + Grafana

#### 2.Exporter和Telegine的比较？

Exporter 是各种独立的资源包，开箱即用，不过呢并不能保证每个 Exporter 都是存活的。

Telegraf 是集中式的，输入输出插件丰富，更容易管理和维护，所以我们用Telegraf来做数据的采集工作。

#### 3.Prometheus、TDengine、InfluxDB的比较？

prometheus适用于单节点的部署，TDengine可以单节点，也可以集群，还免费。InfluxDB的集群是收费的。

但是TDengine也是有缺点的，我踩的最大的坑就是插件在grafana web 的支持不太友好，容易出现各种报错问题。下面会介绍我的环境和软件版本，以及把用到的软件包放到文章末尾的`Q&A`部分，供大家使用，折腾了一周，终于搞定了！

#### 二.版本适配与运行环境

#### 1.组件搭配

我画了一个图，这个图可以明了的看到三个不同软件之间的版本适配情况，这样就可以直接下载对应支持的软件，不用再去翻阅官方文档，节省时间。

![Snipaste_2021-11-10_10-56-58](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/Snipaste_2021-11-10_10-56-58.png)


#### 2.运行环境

我是在单点部署的，下面是用到的环境以及版本说明。

OS：Ubuntu 18.04LTS

Golang: 1.15.6

Telegraf: 1.20.3

TDengine Server: 2.3.1.0

Grafana: 6.2.1

Bailongma: TDengine1.6(Tags)

tdengine-datasource： 3.1.1

### 三.Telegraf介绍与安装

#### 1.介绍

Telegraf是用Go写的代理程序，可以用于收集系统和服务的监控数据，它具备输入插件，可以直接从系统获取指标数据，从第三方API获取指标数据，也具备输出插件，可以将采集的指标发送到各种数据存储，服务和消息队列。比如InfluxDB，Graphite，TDengine，OpenTSDB，Datadog，Librato，Kafka，MQTT，NSQ等等。

#### 2.安装

    wget https://dl.influxdata.com/telegraf/releases/telegraf_1.20.3-1_amd64.deb

    sudo dpkg -i telegraf_1.20.3-1_amd64.deb

###  四.TDengine的介绍与使用

#### 1.介绍

TDengine软件分为服务器、客户端和报警模块三部分，目前2.0版服务器仅能在Linux系统上安装和运行。客户端可以在Windows或Linux上安装和运行。任何OS的应用也可以选择RESTful接口连接服务器taosd。

#### 2.下载安装

官网点击下载，通过邮箱发送link下载。

link:[https://www.taosdata.com/cn/getting-started/#%E9%80%9A%E8%BF%87%E5%AE%89%E8%A3%85%E5%8C%85%E5%AE%89%E8%A3%85](https://www.taosdata.com/cn/getting-started/#%E9%80%9A%E8%BF%87%E5%AE%89%E8%A3%85%E5%8C%85%E5%AE%89%E8%A3%85)

安装

![20211111001359](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111001359.png)

#### 3.下载编译Bailongma

Bailongma 是TDengine提供一个小工具，也叫`taosadapter`。可将Telegraf采集的数据直接写入TDengine，并按规则在TDengine自动创建库和相关表项。

从github下载Bailongma的源码，使用Golang语言编译器编译生成可执行文件。在开始编译前，需要准备好以下条件：

- Linux操作系统的服务器
- 安装好Golang, 1.10版本以上
- 对应的TDengine版本。因为用到了TDengine的客户端动态链接库，因此需要安装好和服务端相同版本的TDengine程序；比如服务端版本是TDengine 2.0.0, 则在bailongma所在的linux服务器（可以与TDengine在同一台服务器，或者不同服务器）

**配置golang，见写过的另一篇文章**

link:[https://luckykang.github.io/2021/01/Golang%E7%B3%BB%E5%88%9701%E4%B8%A8Go%E8%AF%AD%E8%A8%80%E4%BB%8B%E7%BB%8D%E4%B8%8E%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85/](https://luckykang.github.io/2021/01/Golang%E7%B3%BB%E5%88%9701%E4%B8%A8Go%E8%AF%AD%E8%A8%80%E4%BB%8B%E7%BB%8D%E4%B8%8E%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85/)

**下载：**

    git clone https://github.com/taosdata/Bailongma.git

    go mod init Bailongma

**编译：**

    cd blm_telegraf

    go build


#### 4.配置修改telegraf.conf

TDengine 新版本（2.3.0.0+）包含一个 BLM3 独立程序，负责接收包括 Telegraf 的多种应用的数据写入。

首先备份原先的conf文件。

    sudo cp telegraf.conf     telegraf.conf.old

配置方法，在 `/etc/telegraf/telegraf.conf` 增加如下:

    [[outputs.http]]
    url = "http://127.0.0.1:6041/influxdb/v1/write?db=telegraf"
    method = "POST"
    timeout = "5s"
    username = "root"
    password = "taosdata"
    data_format = "influx"
    influx_max_line_bytes = 250

![20211111001225](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111001225.png)

#### 5.启动服务

启动TDengine:

    sudo systemctl start taosd

    sudo systemctl status toasd

![2021-11-08-16-25-58](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-25-58.png)

启动telegraf：

    sudo systemctl start telegraf

![2021-11-08-16-24-55](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-24-55.png)

启动blm_telegraf程序,要和`telegraf.conf`中配置的ip和port一致：

    sudo ./blm_telegraf --host 127.0.0.1 --port 6041

![2021-11-08-16-28-20](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-28-20.png)


**注意**：关于TDengine网络端口说明见文末的`Q&A 1`

#### 6.查看数据

即可在 TDengine 中查询到数据库中写入的数据。

![2021-11-08-16-26-49](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-26-49.png)

获取cpu表中最新的一条数据，发现时序和系统时间一致，说明数据采集与存储工作处于正在运行的状态。下一步，加载到Grafana Web就好啦！！

![20211111000450](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111000450.png)

### 五.Grafana介绍与使用

#### 1.介绍

Grafana是一个开源指标分析和可视化套件，常用于可视化基础设施的性能数据和应用程序分析的时间序列数据。也可以应用于其他领域，包括工业传感器，家庭自动化，天气和过程控制。一般来说，使用Grafana最关心的是如何把数据进行聚合后进行展示。

Grafana支持多种不同的时序数据库数据源，Grafana对每种数据源提供不同的查询方法，而且能很好的支持每种数据源的特性。它支持多种数据源：Graphite、TDengine、Elasticsearch、CloudWatch、InfluxDB、OpenTSDB、Prometheus、MySQL、Postgres、Microsoft SQL Server (MSSQL)等。

#### 2.安装

![20211111005345](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111005345.png)

#### 3.配置 tdengine-datasource

tdengine-datasource是一个taos写的plugin，用于在grafana web中加载TDengine Dashboard。这儿踩了不少坑，包括页面无数据刷新、datasource找不到tdengine插件、找到datasource插件了但是点击保存的时候js报错等问题，应该是taos的插件与grafana的兼容性不太好有关。

我尝试了很多Grafana的版本，包括6.2，7.5，8.2等等。最后使用了6.2，按照下面的步骤配置，终于可以完美展示了。

    wget -c https://github.com/taosdata/grafanaplugin/releases/download/v3.1.1/tdengine-datasource-3.1.1.zip

    sudo unzip tdengine-datasource-3.1.1.zip -d /var/lib/grafana/plugins/

    sudo chown grafana:grafana -R /var/lib/grafana/plugins/

    echo -e "[plugins]\nallow_loading_unsigned_plugins = tdengine-datasource\n" | sudo tee -a /etc/grafana/grafana.ini

    sudo systemctl restart grafana-server.service

![20211111004743](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111004743.png)

![20211111004815](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111004815.png)

#### 4.登录Grafana

打开`http://localhost:3000`,user/pwd都填写`admin`,会提示修改密码

![20211111000246](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111000246.png)

#### 5.导入Dashboard

`Configurations` -> `Data Sources menu`,找到TDengine图标

![20211111001526](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111001526.png)

填写配置信息，添加TDengine Data Source：

![20211111001820](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111001820.png)

点击左侧的`Create`->`Import`,填入 `15146`并加载，会自动加载TDengine 提供的一个Dashboard。

![20211111005506](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111005506.png)

联网情况下，页面会自动刷新。如下图，选择`TDengine`，然后导入。

![20211111002128](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111002128.png)

#### 6.数据展示

按上述步骤操作完成后，现在就可以在页面看到各项收集的监控数据啦

![20211111004527](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111004527.png)

![20211111004556](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111004556.png)

![20211111004627](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111004627.png)

### Q&A

#### 1.关于TDengine网络端口说明

![20211110165110](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211110165110.png)

link：[https://www.taosdata.com/cn/documentation/faq#port](https://www.taosdata.com/cn/documentation/faq#port)

#### 2.环境部署包的下载

![20211111012959](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211111012959.png)