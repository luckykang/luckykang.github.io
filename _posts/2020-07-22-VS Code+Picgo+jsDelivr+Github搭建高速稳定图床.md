---
layout: post
title: "VS Code+Picgo+jsDelivr+Github搭建高速稳定图床"
tag: vs code
---

### 1.创建github仓库，生成token
登录github新建仓库，例如picture_bed

![072231212](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/072231212.png)

打开个人设置，点击settings

![2020-07-22-10-50-23](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-10-50-23.png)

点击developer settings

![2020-07-22-10-52-21](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-10-52-21.png)

点击Personal access tokens

![2020-07-22-10-54-53](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-10-54-53.png)

点击generate new token

![2020-07-22-10-56-32](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-10-56-32.png)

填写名称，勾选repo

![2020-07-22-10-57-32](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-10-57-32.png)

划到底部点击generate  token

![2020-07-22-10-57-53](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-10-57-53.png)

生成新token，保存好这个token，一会要用。

![2020-07-22-11-00-30](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-07-22-11-00-30.png)


### 2.vs code中安装与配置picgo
在vs code扩展中安装picgo，打开扩展设置

![0722130813](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722130813.png)

具体设置如下图：

- 第一项选择 ：github

- 第二项填写分支： master

- 第三项：CDN加速链接的设置，要替换为你的用户名和仓库名

    jsDeliver是免费CDN，放在Github的资源在国内加载速度比较慢，因此需要使用CDN加速来优化网站打开速度，jsDeliver + Github便是免费且好用的CDN，非常适合博客网站使用。

    它的使用方法：https://cdn.jsdelivr.net/gh/你的github用户名/你的仓库名@发布的版本号，比如：https://cdn.jsdelivr.net/gh/luckykang/picture_bed

- 第四项：图片在仓库的存储文件夹，可自定义

- 第五项：github用户名和仓库名

- 第六项：填写前面保存的token

![0722133022](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722133022.png)


### 3.使用picgo上传图片
打开vs code，在文件中 ctrl + alt + E，打开文件夹选择图片上传，

![0722160638](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722160638.png)

上传完成可以看到下图

![0722160740](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722160740.png)

打开github仓库，可以看到图片已经上传成功

![0722160848](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722160848.png)

此时在文件中已经自动生成了链接

![0722160921](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722160921.png)

快捷键

![0722141446](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0722141446.png)


<br>

转载请注明：[康瑶明的博客](https://luckykang.github.io) 







