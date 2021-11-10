---
layout: post
title: Tools系列11丨Telegraf+TDengine+Grafana搭建监控系统
tag: Tools
---


### 一.场景描述和技术选型

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

但是TDengine也是有缺点的，我踩的最大的坑就是数据在grafana web 的展示不够友好，grafana plugin 是taos自己写的，还没有得到grafana的签名认证。

#### 二.系统功能与版本适配

通过这个图，可以看到这三个不同软件之间的分工与配合，能够根据对应支持的版本下载软件。

![Snipaste_2021-11-10_10-56-58](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/Snipaste_2021-11-10_10-56-58.png)

### 三.Telegraf介绍与安装

#### 1.介绍

Telegraf是用Go写的代理程序，可以用于收集系统和服务的监控数据，它具备输入插件，可以直接从系统获取指标数据，从第三方API获取指标数据，也具备输出插件，可以将采集的指标发送到各种数据存储，服务和消息队列。比如InfluxDB，Graphite，TDengine，OpenTSDB，Datadog，Librato，Kafka，MQTT，NSQ等等。

#### 2.安装

    wget https://dl.influxdata.com/telegraf/releases/telegraf_1.20.3-1_amd64.deb

    sudo dpkg -i telegraf_1.20.3-1_amd64.deb

###  四.TDengine的介绍与使用

#### 1.介绍

TDengine软件分为服务器、客户端和报警模块三部分，目前2.0版服务器仅能在Linux系统上安装和运行。客户端可以在Windows或Linux上安装和运行。任何OS的应用也可以选择RESTful接口连接服务器taosd。

#### 2.安装

    wget -qO - http://repos.taosdata.com/tdengine.key | sudo apt-key add -

    echo "deb [arch=amd64] http://repos.taosdata.com/tdengine-stable stable main" | sudo tee /etc/apt/sources.list.d/tdengine-stable.list

    sudo apt-get update

    apt-get policy tdengine

    sudo apt-get install tdengine

    # 查看版本 taos

#### 3.下载编译Bailongma

Bailongma是TDengine提供一个小工具，可将Telegraf采集的数据直接写入TDengine，并按规则在TDengine自动创建库和相关表项。

从github下载Bailongma的源码，使用Golang语言编译器编译生成可执行文件。在开始编译前，需要准备好以下条件：

- Linux操作系统的服务器
- 安装好Golang, 1.10版本以上
- 对应的TDengine版本。因为用到了TDengine的客户端动态链接库，因此需要安装好和服务端相同版本的TDengine程序；比如服务端版本是TDengine 2.0.0, 则在bailongma所在的linux服务器（可以与TDengine在同一台服务器，或者不同服务器）

下载：

    git clone https://github.com/taosdata/Bailongma.git

    go mod init Bailongma
编译：

    cd blm_telegraf

    go build


#### 4.配置修改telegraf.conf

TDengine 新版本（2.3.0.0+）包含一个 BLM3 独立程序，负责接收包括 Telegraf 的多种应用的数据写入。

配置方法，在 `/etc/telegraf/telegraf.conf` 增加如下:

    [[outputs.http]]
    url = "http://127.0.0.1:6041/influxdb/v1/write?db=telegraf"
    method = "POST"
    timeout = "5s"
    username = "root"
    password = "taosdata"
    data_format = "influx"
    influx_max_line_bytes = 250

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

即可在 TDengine 中查询到数据库中 Telegraf 写入的数据。

![2021-11-08-16-26-49](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-26-49.png)


### 五.Grafana介绍与使用

#### 1.介绍

Grafana是一个开源指标分析和可视化套件，常用于可视化基础设施的性能数据和应用程序分析的时间序列数据。也可以应用于其他领域，包括工业传感器，家庭自动化，天气和过程控制。我使用Grafana最关心的是如何把数据进行聚合后进行展示。

Grafana支持多种不同的时序数据库数据源，Grafana对每种数据源提供不同的查询方法，而且能很好的支持每种数据源的特性。它支持多种数据源：Graphite、TDengine、Elasticsearch、CloudWatch、InfluxDB、OpenTSDB、Prometheus、MySQL、Postgres、Microsoft SQL Server (MSSQL)。

#### 2.安装

    wget -q -O - https://packages.grafana.com/gpg.key |\
    sudo apt-key add -
    
    echo "deb https://packages.grafana.com/oss/deb stable main" |\
    sudo tee -a /etc/apt/sources.list.d/grafana.list

    sudo apt-get update

    sudo apt-get install grafana

#### 3. TDengine Data Source Plugin（和4比较）

    git clone --depth 1 https://github.com/taosdata/grafanaplugin.git

    sudo mkdir -p /var/lib/grafana/plugins/tdengine

    sudo cp -rf dist/* /var/lib/grafana/plugins/tdengine

![2021-11-08-16-29-30](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-29-30.png)

#### 4.配置 tdengine-datasource

    wget -c https://github.com/taosdata/grafanaplugin/releases/download/v3.1.1/tdengine-datasource-3.1.1.zip

    sudo unzip tdengine-datasource-3.1.1.zip -d /var/lib/grafana/plugins/

    sudo chown grafana:grafana -R /var/lib/grafana/plugins/tdengine

    echo -e "[plugins]\nallow_loading_unsigned_plugins = tdengine-datasource\n" | sudo tee -a /etc/grafana/grafana.ini

    sudo systemctl restart grafana-server.service

#### 5.登录Grafana

打开`http://localhost:3000`,user/pwd都填写`admin`,会提示修改密码

添加TDengine Data Source：

`Configurations` -> `Data Sources menu`,点击`Add data source`按钮


#### 6.导入json

下载 dashboard JSON 文件后导入

[https://github.com/taosdata/grafanaplugin/blob/master/examples/telegraf/grafana/dashboards/telegraf-dashboard-v0.1.0.json](https://github.com/taosdata/grafanaplugin/blob/master/examples/telegraf/grafana/dashboards/telegraf-dashboard-v0.1.0.json) 


### Q&A

#### 1.关于TDengine网络端口说明

![20211110165110](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211110165110.png)

link：[https://www.taosdata.com/cn/documentation/faq#port](https://www.taosdata.com/cn/documentation/faq#port)