---
layout: post
title: Python系列02 | anaconda常用命令
tag: python
---

# 一.安装与卸载

## 1. 安装anaconda

```
bash Anaconda3-2022.02-Linux-x86_64.sh
```
按`ENTER`继续,输入`yes`接受协议，并且你会被提示选择安装路径,然后按`ENTER`确认位置。

安装过程将会花费一些时间，并且一旦完成，脚本将会问你是否想要运行`conda init`。输入`yes`,

```
Installation finished.
Do you wish the installer to initialize Anaconda3
by running conda init? [yes|no]
```
这将会将命令行工具conda添加到系统的PATH环境变量中。

想要激活 Anaconda，你可以关闭并且重新打开你的 shell 或者在当前 shell 会话中输入下面的命令，来重新加载PATH环境变量：

```
source ~/.bashrc
```

如果你在一个桌面系统上安装 Anaconda，在终端输入`anaconda-navigator`打开Navigator GUI。

## 2.升级与卸载anaconda

```
# 升级
conda update --all
# 卸载
rm -rf ~/anaconda3 ~/.condarc ~/.conda ~/.continuum
打开~/.bashrc，并且从环境变量中移除 Anaconda
```

# 二.conda管理

## 1.查看conda配置

```
conda config --show
```
## 2.设置镜像

```
#设置清华镜像
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
#设置bioconda
conda config --add channels bioconda
conda config --add channels conda-forge
#设置搜索时显示通道地址
conda config --set show_channel_urls yes
```
## 3.更新conda

```
conda update conda
```

## 4.更新Anaconda整体

```
conda update Anaconda
```

# 三.环境管理

## 1.创建虚拟环境

这表示创建python版本为3.8、名字为`env_name`的虚拟环境。创建后，`env_name`文件可以在Anaconda安装目录envs文件下找到。在不指定python版本时，自动创建基于最新python版本的虚拟环境.      

```
conda create -n env_name python=3.8
```
## 2.创建虚拟环境的同时安装必要的包

```
conda create -n env_name numpy matplotlib python=3.8
```

## 3.查看虚拟环境有哪些

星号的位置表示我现在该环境下工作。

```
conda env list
conda info -e
conda info --envs
```
## 4.激活虚拟环境

```
conda activate env_name
```

## 5.退出虚拟环境

```
conda deactivate
```

## 6.删除虚拟环境

执行以下命令可以将该指定虚拟环境及其中所安装的包都删除。

```
conda remove --name env_name --all
```

如果只删除虚拟环境中的某个或者某些包则是：
```
conda remove --name env_name  package_name
```
## 7.导出环境

```
#获得环境中的所有配置
conda env export --name myenv > myenv.yml
#重新还原环境
conda env create -f  myenv.yml
```

# 四.包管理

## 1.查询包的安装情况

```
conda list
```

## 2.包的安装与更新

```
conda install numpy
conda update numpy
```

## 3.卸载包

这样会将依赖于这个包的所有其它包也同时卸载。

```
conda uninstall package_name
```

如果不想删除依赖其当前要删除的包的其他包,不过一般不建议这样，会使得环境有些破碎。如果一个包A被删除了，而依赖于它的包B、C等却没有删除，但是那些包其实也已经不可用了。另一方面，之后你又安装了A的新版本，而不幸的是，B、C却与新版本的A不兼容因此依然是不可用的。

```
conda uninstall package_name --force
```

## 4.清理anaconda缓存

```
conda clean -p      # 删除没有用的包 --packages
conda clean -t      # 删除tar打包 --tarballs
conda clean -y -all # 删除所有的安装包及cache(索引缓存、锁定文件、未使用过的包和tar包)
```

# 五.python版本的管理

## 1.将版本变更到指定版本

```
conda install python=3.8
# 更新完后可以用以下命令查看变更是否符合预期。
python -V
```

## 2.将python版本更新到最新版本

```
conda update python
```