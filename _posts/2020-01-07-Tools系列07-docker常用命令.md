---
layout: post
title: Tools系列07-docker常用命令
tag: Tools
---

### 1.列出、查看所有容器

查看所有容器ID

```
docker ps -aq
```

查看所有运行或者不运行容器

```
docker ps -a
```

### 2.停止所有的container

这样才能够删除其中的images

```
docker stop $(docker ps -a -q) 或者 docker stop $(docker ps -aq) 
```

### 3.删除所有container

```
docker rm $(docker ps -a -q) 
```

或者

```
docker rm $(docker ps -aq) 
```
删除所有停止的容器
```
docker container prune
```

### 4.查看images

```
docker images
```

### 5.删除images

```
docker rmi <image id>
```

想要删除untagged images，也就是那些id为的image的话可以用
```
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```
要删除全部image（镜像）的话
```
docker rmi $(docker images -q)
```
强制删除全部image的话
```
docker rmi -f $(docker images -q)
```

删除所有不使用的镜像
```
docker image prune --force --all或者docker image prune -f -a
```

### 6.主机和容器的相互复制

从容器到宿主机复制
```
 docker cp tomcat：/webapps/js/text.js /home/admin
 docker  cp 容器名:  容器路径       宿主机路径         
```
从宿主机到容器复制
```
 docker cp /home/admin/text.js tomcat：/webapps/js
 docker cp 宿主路径中文件      容器名  容器路径   
```

### 7.停止、启动、杀死、重启一个容器

```
docker stop Name或者ID  
docker start Name或者ID  
docker kill Name或者ID  
docker restart name或者ID
```
### 8.docker进入容器，查看配置文件

```
docker exec ：在运行的容器中执行命令
        -d :分离模式: 在后台运行
        -i :即使没有附加也保持STDIN（标准输入） 打开,以交互模式运行容器，通常与 -t 同时使用；
        -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
docker exec -it  f94d2c317477 /bin/bash
```

### 9.修改配置、退出容器

1、如果要正常退出不关闭容器，请按Ctrl+P+Q进行退出容器

2、如果使用exit退出，那么在退出之后会关闭容器，可以使用下面的流程进行恢复

使用docker restart命令重启容器

使用docker attach命令进入容器
