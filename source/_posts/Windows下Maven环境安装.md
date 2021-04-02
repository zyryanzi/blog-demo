---
title: Windows下Maven环境安装
date: 2021-03-18 14:53:40
tags:
    - Windows
    - Maven

categories:
    - 计算机科学
    - Maven
---

*Maven安装，方便自己，方便他人*
<!--more-->

### 下载
官网下载就不错

    http://maven.apache.org/download.cgi#
{% asset_img download.jpg JDK下载 %}

### 安装
找一个你喜欢的角落，解压她，解压即安装。

### 环境变量
**MAVEN_HOME**
我的电脑 -> 属性 -> 高级 -> 系统设置 -> 新建
{% asset_img envconf.jpg 配置MAVEN_HOME %}
**path**
在系统变量中双击 path，将%MAVEN_HOME%\bin 写入
{% asset_img envpath.jpg 配置path %}

### 验证
重新打开 命令行工具 输入

    mvn --version

有maven信息输出，至此安装完成。
