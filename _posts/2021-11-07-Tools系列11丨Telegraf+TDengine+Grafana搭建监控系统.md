---
layout: post
title: Tools系列11丨Telegraf+TDengine+Grafana搭建监控系统
tag: Tools
---

#### 一.监控系统架构图

![Snipaste_2021-11-10_10-56-58](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/Snipaste_2021-11-10_10-56-58.png)

### 二.Telegraf介绍与安装

#### 1.介绍

Telegraf是用Go写的代理程序，可以用于收集系统和服务的监控数据，它具备输入插件，可以直接从系统获取指标数据，从第三方API获取指标数据，也具备输出插件，可以将采集的指标发送到各种数据存储，服务和消息队列。比如InfluxDB，Graphite，TDengine，OpenTSDB，Datadog，Librato，Kafka，MQTT，NSQ等等。

#### 2.安装

    wget https://dl.influxdata.com/telegraf/releases/telegraf_1.20.3-1_amd64.deb

    sudo dpkg -i telegraf_1.20.3-1_amd64.deb

###  三.TDengine的介绍与使用

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

TDengine提供一个小工具Bailongma，只需在Telegraf做简单配置，无需任何代码，就可将Telegraf采集的数据直接写入TDengine，并按规则在TDengine自动创建库和相关表项。

从github下载Bailongma的源码，使用Golang语言编译器编译生成可执行文件。在开始编译前，需要准备好以下条件：

- Linux操作系统的服务器
- 安装好Golang, 1.10版本以上
- 对应的TDengine版本。因为用到了TDengine的客户端动态链接库，因此需要安装好和服务端相同版本的TDengine程序；比如服务端版本是TDengine 2.0.0, 则在bailongma所在的linux服务器（可以与TDengine在同一台服务器，或者不同服务器）

下载：

    git clone https://github.com/taosdata/Bailongma.git

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


#### 6.查看数据

即可在 TDengine 中查询到数据库中 Telegraf 写入的数据。

![2021-11-08-16-26-49](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2021-11-08-16-26-49.png)


### 四.Grafana介绍与使用

#### 1.介绍

Grafana是一个开源指标分析和可视化套件，常用于可视化基础设施的性能数据和应用程序分析的时间序列数据。也可以应用于其他领域，包括工业传感器，家庭自动化，天气和过程控制。我使用Grafana最关心的是如何把数据进行聚合后进行展示。

Grafana支持多种不同的时序数据库数据源，Grafana对每种数据源提供不同的查询方法，而且能很好的支持每种数据源的特性。它支持多种数据源：Graphite、Elasticsearch、CloudWatch、InfluxDB、OpenTSDB、Prometheus、MySQL、Postgres、Microsoft SQL Server (MSSQL)。

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

打开`http://localhost:3000`,user/psd都填写`admin`,会提示修改密码

添加TDengine Data Source：

`Configurations` -> `Data Sources menu`,点击`Add data source`按钮


#### 6.导入json

下载 dashboard JSON 文件后导入

[https://github.com/taosdata/grafanaplugin/blob/master/examples/telegraf/grafana/dashboards/telegraf-dashboard-v0.1.0.json](https://github.com/taosdata/grafanaplugin/blob/master/examples/telegraf/grafana/dashboards/telegraf-dashboard-v0.1.0.json) 