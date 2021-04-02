---
title: Windows环境下geoserver服务的部署
date: 2021-03-18 11:06:59
tags:
    - Windows
    - Geoserver
categories:
    - 计算机科学
    - GIS
---

*`Geoserver` 作为一款开源的 `GIS` 软件，使广大地勘从业者受益颇深；*
*利用 `GeoServer` 可以方便地发布地图数据，允许用户对特征数据进行更新、删除、插入操作；*
*通过 `GeoServer` 可以比较容易地在用户之间迅速共享空间地理信息。*
<!--more-->
### 环境搭建
Geoserver 美中不足的地方是使用 Java 语言编写，运行起来非常消耗资源
推荐使用`JDK1.8+`，否则可能有问题。
#### JDK 安装
JDK 的安装请参见：
https://nontech.top/2021/03/18/Windows环境下geoserver服务的部署/
#### Maven 安装
Maven 的安装请参见：
https://nontech.top/
Maven 安装完毕，待会儿如果有依赖不能下载完全，就需要添加镜像了，找到具体仓库的办法参见：
https://nontech.top/

#### Git 安装
Git 的安装请参见：
https://nontech.top/

#### IDE
IDE 推荐使用 IDEA，安装及破解参见：
https://www.exception.site/essay/how-to-free-use-intellij-idea-2019-3

### 源码下载
http://geoserver.org/
务必选择稳定版
{% asset_img geoserverindex.jpg Geoserver 首页 %}
进入对应版本github页面
{% asset_img codedownload.jpg GitHub 页面 %}
下载源码
{% asset_img gitdownload.jpg 下载源码 %}
回到本地打开 git-bash ，然后进入到一个你喜欢的路径下使用

    git clone https://github.com/geoserver/geoserver.git

### Maven 安装启动
源码下载完毕后，进入`.\geoserver\src`，并执行下述命令

    mvn clean install -Dmaven.test.skip=true

如果不跳过test, 会验证很久，很讨厌。
安装完成，即可启动，但是在启动之前先查看一下 8080 端口是否被占用

    netstat -ano | grep 8080

如果被占用可在如下文件中修改，快选个吉利数字吧，不支持双色球长度
{% asset_img changeport.jpg 修改端口 %}
好了，现在可以启动了，右键点击刚刚的`Start` -> `run'Start.main()'`
{% asset_img run1st.jpg 首次运行 %}
第一次运行会报错，具体报什么我忘了，要修改一下启动配置
{% asset_img confpos.jpg 这里修改配置 %}
在下图中红框中的尾巴上加上 `\web\app`
{% asset_img changeconf.jpg 修改配置 %}
再启动就好了。可以通过浏览器访问，初始用户名/密码：admin/geoserver

    localhost:[port]/geoserver

### 使用
**不会！！**