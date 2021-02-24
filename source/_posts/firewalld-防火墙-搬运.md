---
title: firewalld-防火墙 - 搬运
date: 2021-02-01 16:19:56
tags: 
    - Linux
    - firewalld
    - 防火墙
    - 命令
categories: 
    - 计算机科学
    - 系统
---

*他山之玉，补己不足*
<!--more-->

## 基本命令：

    //启动
    systemctl start firewalld

    //查看状态
    systemctl status firewalld

    //停止
    systemctl stop firewalld

    //开机启用
    systemctl enable firewalld

    //开机禁用
    systemctl disable firewalld

## 端口：

开放/关闭端口：

    // 开放
    firewall-cmd --zone=public --add-port=80/tcp --permanent

    //关闭
    firewall-cmd --zone= public --remove-port=80/tcp --permanent
    --permanent(加上是永久生效)
    
    //重载
    firewall-cmd --reload（新增之后一定要重载才生效）
    

查看开放的服务和端口：

    firewall-cmd --list-services
    firewall-cmd --zone=public --list-ports

## firewalld-cmd其他操作：
 
    //查看版本
    firewall-cmd --version

    //查看帮助
    firewall-cmd --help

    //显示状态
    firewall-cmd --state

    //查看所有打开的端口
    firewall-cmd --zone=public --list-ports

    //更新防火墙规则
    firewall-cmd --reload

    //查看区域信息
    firewall-cmd --get-active-zones

    //查看指定接口所属区域
    firewall-cmd --get-zone-of-interface=eth0

    //拒绝所有包
    firewall-cmd --panic-on

    //取消拒绝状态
    firewall-cmd --panic-off

    //查看是否拒绝
    firewall-cmd --query-panic

## Tips：
1. systemctl是CentOS7的主要服务管理工具，融合了service和chkconfig的功能。
2. 经常本地好好的，到远程就不行，多半是防火墙或权限的问题。
3. 尽量不要关闭防火墙，有问题还是解决问题优先。
4. firewalld 和 iptables 据说是很类似的东西，有机会研究下。