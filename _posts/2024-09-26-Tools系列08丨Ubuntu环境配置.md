---
layout: post
title: Tools系列08丨Ubuntu环境配置
tag: Tools
---


## 1.搜狗输入法 ,可以调整输入法字体大小

[https://shurufa.sogou.com/linux/guide](https://shurufa.sogou.com/linux/guide)   

打开`sudo vim /etc/environment`，添加

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
```
重启系统即可。

```
# delete
sudo apt-get purge sogoupinyin
```
## 2.谷歌输入法

```
# install
sudo apt install fcitx-googlepinyin -y
fcitx-config-gtk3

# delete
sudo apt-get purge fcitx-googlepinyin -y
```

## 3.双系统ntp时间同步
```
sudo apt-get install ntpdate			//在Ubuntu下更新本地时间
sudo ntpdate time.windows.com
sudo hwclock --localtime --systohc		//将本地时间更新到硬件上
```
## 4.python国内源
```
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
pip install --upgrade pip
```
## 5.v2rayA 使用
```
sudo snap install v2raya
https://pengtech.net/network/v2rayA_install.html
```
系统配置：
![20240927090958](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20240927090958.png)


## 6.h264格式的视频播放
```
sudo apt-get install gstreamer1.0-plugins-bad
```

## 7.可以上网，但是显示问号
设置-隐私，关闭连接检查

## 8.显示器改为x11
```
sudo vim  /etc/gdm3/custom.conf    取消WaylandEnable=false 的注释，然后重启系统
sudo apt install --reinstall xserver-xorg 
```

## 9.外放没声音 
[参考链接：https://cloud.tencent.com/developer/ask/sof/116483001](https://cloud.tencent.com/developer/ask/sof/116483001)

```
sudo chmod -R a+rwx /dev/snd
ls -la /dev/snd
systemctl --user status pipewire-session-manager.service
systemctl --user daemon-reload
sudo apt install firmware-sof-signed
sudo apt install alsa-ucm-conf
#把 options snd-hda-intel dmic_detect=0 添加到/etc/modprobe.d/alsa-base.conf并重新启动
sudo vim /etc/modprobe.d/alsa-base.conf 
sudo reboot

# snipaste 使用
sudo chmod +x Snipaste-2.8.9-Beta-x86_64.AppImage
sudo add-apt-repository universe
sudo apt update -y 
sudo apt install libfuse2 -y
./Snipaste-2.9.2-Beta-x86_64.AppImage
开机启动
打开启动应用程序，添加名称：snipaste, 命令： /home/siasun/Downloads/Snipaste-2.9.2-Beta-x86_64.AppImage
```

## 10.typora 破解版 

```
git clone https://github.com/vconlln/linux-typora.git
cd linux-typora
cargo build --bin node-inject --release
mv target/release/node-inject /usr/share/typora
cd /usr/share/typora
sudo ./node-inject
cd ~/Downloads/linux-typora/
cargo run --bin license-gen --release
然后打开软件填入生成的激活密钥：T9M248-SKWVP-8AKZT-QNWERT
```


