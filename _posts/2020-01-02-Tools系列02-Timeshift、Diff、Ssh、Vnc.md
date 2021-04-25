---
layout: post
title: Tools系列02-Timeshift、Diff、Ssh、Vnc
tag: Tools
---
vnc


### 1.git diff

#### 生成补丁

```
git init 
git add .
git status 

# 修改完内容后,生成patch
git diff > a.diff
```

#### 打补丁

```
# 先检查是否报错
git diff --check apply a.diff  

# 如果没报错就可以打   
git diff apply a.diff
```

### 2.timeshift

```
sudo add-apt-repository -y ppa:teejee2008/ppa

sudo  apt update

sudo  apt install timeshift
```

### 3.ssh-server

```
# 安装
sudo apt install openssh-server

# 查看安装的服务
dpkg -l | grep ssh

#确认ssh-server是否启动
ps -e | grep ssh
```

### 4.vnc-server

- 1.在设置中允许进行远程控制，访问密码可自由设置，可不使用系统的登录密码，该选项要呈开启状态。

![20210425121406](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425121406.png)

- 2.安装vnc基础服务

```
sudo apt install xrdp vnc4server xbase-clients
```

- 3.安装`conf-editor`(取消权限限制)

我们需要取消请求加密的功能，否则缺少这一步是无法远程连接的。

```
sudo apt install dconf-editor
```

安装完成后我们要打开dconf-editor工具，在桌面搜索dconf-editor打开

![20210425120810](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425120810.png)

打开后在org-->gnome-->desktop-->remote-access，关闭`requlre-encryption`,至此前期准备工作已经完成，建议开启`lock-screen-on-disconnect`,当断开连接的时候会自动锁屏。接下来可以通过VNC工具或者Windows自带的远程桌面进行访问了。

![20210425121014](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425121014.png)

- 5.使用Windows自带的远程桌面进行访问，打开后输入IP，点击连接

![20210425112738](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425112738.png)

选择模式`vnc-any`,端口不用改变，输入IP和在ubuntu系统`desktop share`设置的远程密码进行登录。

![20210425112943](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425112943.png)

成功连接

![20210425120342](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425120342.png)


- 6.使用VNC Viewer访问，连接方式选择为`Prefer on`，输入IP和Name

![20210425122526](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425122526.png)

点击图标后，弹出会话框中填写第一步设置的访问密码

![20210425123202](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425123202.png)

就可以访问了

![20210425123801](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210425123801.png)

