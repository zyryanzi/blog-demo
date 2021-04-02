---
title: Win10-Nexus
date: 2021-03-15 14:04:12
tags:
    - Windows
    - Nexus
    - Maven
categories:
    - 计算机科学
    - Java
---

*在使用 maven 管理依赖包的过程中，经常会遇到配置资源库或是镜像中没有依赖，需要自己手动导入，这样很麻烦，所以就用到了 nexus 这个强大的私服工具*
*自己搭建私服来管理自己经常用到的依赖，方便快捷不罗嗦，真香*
<!--more-->
### 下载 安装
想要安装先下载 `Nexus Repository Manager`
https://help.sonatype.com/repomanager3/download/download-archives---repository-manager-3
{% asset_img download.jpg 下载 %}
下载红框中版本，解压到一个你喜欢的位置，即是安装。
sonatype-work（工作目录），nexus-3.30.0-01（安装文件目录）。

### 启动 访问
进入 `nexus-3.30.0-01\bin\`，输入 `nexus.exe/run`

启动后出现如下字样，即为成功：
{% asset_img succ.jpg 安装成功 %}

通过浏览器访问：http://localhost:8081/
初始用户名: admin, 密码：admin123

### 初步配置
依次点击 `左侧repositories`、`上方齿轮图标`，编辑名为`maven-central`的条目，修改其中的 Remote storage，完成首次配置；
即可开始使用。