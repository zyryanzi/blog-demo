---
title: centos7-nginx-setup
date: 2021-02-12 18:04:22
tags: 
    - Linux
    - nginx
categories: 
    - 计算机科学
    - 网络技术
    - nginx
---

*在爬楼安装 nginx 的过程中，遇到了不少问题，记录于此，已备不时之需*
<!--more-->

## 安装准备
### 下载 nginx 源码
    进入目标路径，例如 /usr/local
    yum install -y wget
    wget http://nginx.org/download/nginx-1.9.9.tar.gz

### 检查/安装 gcc(编译器)
    gcc -v
    yum -y install gcc

### 安装 pcre、pcre-devel （为什么安装俩？）
    yum install -y pcre pcre-devel
pcre 是一个 perl 库，包括 perl 兼容的正则表达式库，nginx 的 http 模块使用 pcre 来解析正则表达式

### 安装 zlib
    yum install -y zlib zlib-devel
zlib 库提供了很多种压缩和解压缩方式，nginx 使用 zlib 对 http 包的内容进行 gzip

### 安装 openssl
    yum install -y openssl openssl-devel
openssl是web安全通信的基石，大神说禁止裸奔

## 安装 nginx
### 解压 nginx
    tar -zxvf  nginx-1.9.9.tar.gz
    并进入 nginx-1.9.9
### make nginx
~ 执行 ./configure, 如果有需要的模块，跟在后面，例如：

    ./configure --with-http_image_filter_module

可能会报错，关于 GD 的，那么需要执行下面的语句，再执行 ./configure --xxx

    yum install -y gd gd-devel

~ 依次执行 make，make install

### 检查配置文件
查看 /usr/local/nginx/conf/nginx.conf，80端口是否配置；
进入 /usr/local/nginx/sbin 目录，执行命令启动nginx, 并查看是否成功:

    ./nginx
    ps -ef | grep nginx

### 访问服务器IP
    出现： Welcom to nginx 页面

## 环境变量
因为经常要执行 nginx 命令，每次都进入 nginx 文件夹很不方便，因此配置环境变量会方便些，执行

    vim /etc/profile

在末尾添加两条语句：

    PATH=$PATH:/usr/local/nginx/sbin
    export PATH

保存退出后，需要执行一条语句使修改生效：

    source /etc/profile

此后执行命令会方便很多，如：

    nginx -t
    nginx -s reload
    nginx -s stop

## 配置服务（规范启停服务操作）
新建并编辑服务文件：

    vim /usr/lib/systemd/system/nginx.service

    内容如下：
    [Unit]
    Description=nginx - high performance web server
    After=network.target remote-fs.target nss-lookup.target

    [Service]
    Type=forking
    PIDFile=/usr/local/nginx/logs/nginx.pid
    ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
    ExecStart=/usr/local/nginx/sbin/nginx
    ExecReload=/usr/local/nginx/sbin/nginx -s reload
    ExecStop=/usr/local/nginx/sbin/nginx -s stop
    PrivateTmp=true

    [Install]
    WantedBy=multi-user.target

保存退出（我试了，停止不了，也许我哪里搞得不对，慢慢再看了）

## 完成
*参考链接：https://blog.csdn.net/qq_37345604/article/details/90034424*