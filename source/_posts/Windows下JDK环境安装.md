---
title: Windows下JDK环境安装
date: 2021-03-18 12:08:56
tags:
    - jdk

categories:
    - 计算机科学
    - Java
---

*JDK环境安装，方便自己，方便他人*
<!--more-->

### 下载
官网下载就不错

    https://www.oracle.com/java/technologies/javase-jdk16-downloads.html
{% asset_img jdkdownload.jpg JDK下载 %}

### 安装
一切默认安装即可，jre也要安装呦，装在jdk目录下。

### 环境变量
**JAVA_HOME**
我的电脑 -> 属性 -> 高级 -> 系统设置 -> 新建
{% asset_img envconf.jpg 配置JAVA_HOME %}
**path**
在系统变量中双击 path，将%JAVA_HOME%\bin 写入
{% asset_img envpath.jpg 配置path %}

### 验证
重新打开 命令行工具 输入

    java -version

有java信息输出，至此安装完成。

