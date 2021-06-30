---
layout: post
title: Tools系列08-不同浏览器书签直接同步：坚果云+Floccus_详细使用教程
tag: Tools
---

### 1.说明

平常用的最多的两款浏览器呢，是edge和chrome。edge有个好处，就是账号可以同步一。但是呢，用过一段时间就发现真的没有chrome顺手，还是会有各种小问题，比如有时候网页加载问题、下载文件格式问题等。chrome的账号同步由于国内网络原因无法同步，就很痛苦。不同的浏览器之间想同步书签，就需要手动导来导去，不太方便，如果丢失了就再也找不回来了。怎么办呢？

我这里找到一款稳定好用的书签同步解决方案。

网上呢，很多人推荐的是采用EverSync这个插件，因为听说了Xmarks的遭遇，所以对这种第三方同步的不是很放心，就想找一个能书签数据保存在自己网盘的同步方案，然后就找到了这个开源的`floccus`。floccus支持火狐和Chrome、Edge浏览器的书签同步，还是很方便的。

### 2.floccus介绍

floccus是一个开源的，通过Nextcloud、WEbDAV、本地文件（或者任何同步文件）的不同浏览器书签同步解决方案。我采用的是WebDAV方案。

开源地址：[https://github.com/marcelklehr/floccus](https://github.com/marcelklehr/floccus)

### 3.坚果云上设置步骤

在同步文件夹根目录下创建一个文件夹，默认不同步到本地。

![20210623143433](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623143433.png)

然后上传一个`bookmarks.xbel`文件到刚才新建的文件夹里面，没有的点这里下载。[https://pan.baidu.com/s/14-P_VPG-qwQTUkJWXZpjFQ 
](https://pan.baidu.com/s/14-P_VPG-qwQTUkJWXZpjFQ 
)

提取码：qiwc 

![20210623143453](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623143453.png)

接着坚果云“账户信息”-“安全选项”-“第三方应用管理”，创建一个WebDAV应用，获取密码。

![20210623144417](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623144417.png)

### 4.Floccus设置同步书签到坚果云

安装很简单，在谷歌商店，github或者国内第三方插件网站可以安装。主要介绍如何设置绑定账户并把书签同步到坚果云中。这里建议关闭浏览器的自动同步，否则可能引起冲突。

安装完成后点击图标的设置按钮

![20210623145441](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623145441.png)

弹出页面填入坚果云提供的相关信息，在`Bookmarks file path`  填入我们的路径文件`floccus/bookmarks.xbel`，然后选择浏览器文件为`本地文件`，路径选择`根路径`，点击保存。

![20210623144749](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623144749.png)

配置完成点击同步，就会把你浏览器上所有书签同步到坚果云。

![20210623145638](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623145638.png)

### 5.在新的浏览器如何同步坚果云保存的书签

首先要在同步书签的浏览器导出数据，点击按钮

![20210623150056](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623150056.png)

点击导出

![20210623150310](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623150310.png)

找到导出文件

![20210623150353](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623150353.png)

我这里要导入Edge浏览器，在浏览器安装完成Floccus后点击导入按钮

![20210623150627](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623150627.png)

点击导入

![20210623150703](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623150703.png)

点击右下角的`所有文件类型`，然后选中我们的文件。

![20210623150803](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623150803.png)

这时候Floccus已经加载了我们的文件，然后点击图标，就可以看到我们的个人信息，点击同步就可以看到书签已经同步到Edge浏览器了。

![20210623151032](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623151032.png)

我们可以把这个`floccus.export.txt`保存在网盘。之后每次在新的浏览器上安装了Floccus，我们就可以从网盘找到这个文件，导入到floccus中就可以了。


#### 注意

如果配置错了想要删除这个同步账号，按下图点击即可。

![20210623151411](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210623151411.png)