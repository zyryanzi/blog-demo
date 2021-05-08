---
title: docker commit 的一点常识
date: 2021-04-08 14:43:48
tags: 
    - docker

categories:
    - 计算机科学
    - Docker
---

*当您使用别人的镜像不是很舒畅的时候，当您想要加入一点东西到现有镜像中时，您需要这条命令 `docker commit`*
<!--more-->

### docker 容器的交互
    docker run -it [image_name]:[version] bash
    e.g.
    docker run -it ubuntu:16.04 bash
    请勿加入 `-d` 参数

### 执行变更
e.g.

    apt-get install -y vim

### 退出容器
    exit

### 打包镜像
    docker commit container_id ubuntu-vim

### 后续查看
查看镜像

    docker images | grep ubuntu-vim

查看镜像的详细信息：

    docker inspect ubuntu-vim:image_id

使用新镜像

    docker run -it ubuntu-vim bash

查看是否还有vim
    
    vim --version

### 参数说明
-a :提交的镜像作者；
-c :使用Dockerfile指令来创建镜像；
-m :提交时的说明文字；
-p :在commit时，将容器暂停。

将容器 [container_id] 保存为新的镜像,并添加提交人信息和说明信息。
docker commit -a "author" -m "description" [container_id] [image_name]:[version]
