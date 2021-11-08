---
layout: post
title: Tools系列11丨Telegraf+TDengine+Grafana搭建监控系统
tag: Tools
---

###  一.TDengine的介绍与使用



#### 1.TDengine是什么？有什么用

TDengine软件分为服务器、客户端和报警模块三部分，目前2.0版服务器仅能在Linux系统上安装和运行.客户端可以在Windows或Linux上安装和运行。任何OS的应用也可以选择RESTful接口连接服务器taosd。



#### 2.安装与启动

下载链接(需要填写邮箱获取下载链接)：[https://www.taosdata.com/cn/all-downloads/](https://www.taosdata.com/cn/all-downloads/)

 


### 二.Telegraf介绍与安装

#### 1.Telegraf是什么，可以用来干什么

elegraf是用Go写的代理程序，可以用于收集系统和服务的统计数据，是TICK技术栈的一部分。它具备输入插件，可以直接从系统获取指标数据，从第三方API获取指标数据，甚至可以通过statsd和Kafka获取指标数据。它还具备输出插件，可以将采集的指标发送到各种数据存储，服务和消息队列。比如InfluxDB，Graphite，TDengine，OpenTSDB，Datadog，Librato，Kafka，MQTT，NSQ等等。

#### 2.安装

    wget https://dl.influxdata.com/telegraf/releases/telegraf_1.20.3-1_amd64.deb

    sudo dpkg -i telegraf_1.20.3-1_amd64.deb

安装完成后先不要启动，后边需要配置`telegraf.conf`。配置文件位置为：`/etc/telegraf/telegraf.conf`


### 三.Grafana

#### 1.Grafana是什么，有什么用

Grafana是一个开源指标分析和可视化套件，常用于可视化基础设施的性能数据和应用程序分析的时间序列数据。也可以应用于其他领域，包括工业传感器，家庭自动化，天气和过程控制。我使用Grafana最关心的是如何把数据进行聚合后进行展示。

Grafana支持多种不同的时序数据库数据源，Grafana对每种数据源提供不同的查询方法，而且能很好的支持每种数据源的特性。它支持多种数据源：Graphite、Elasticsearch、CloudWatch、InfluxDB、OpenTSDB、Prometheus、MySQL、Postgres、Microsoft SQL Server (MSSQL)。

