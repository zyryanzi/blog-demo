---
title: git-bash 配置本地代理
date: 2021-03-04 17:34:25
tags:
    - git
    - git-bash
    - proxy
categories: 
    - 计算机科学
    - git
---

*帝都的宇宙神秘大会真的是让人头痛不已，`github`的连接极度不稳，无奈之下只好用搭好的梯子设置个本地代理*
<!--more-->

### 基础设施
使用本地代理，首先有个搭好的梯子，本地还要有一个梯子客户端。
本地的客户端可以将梯子的服务暴露出来各种协议的端口，假设暴露出来的是6789

### Http加速
#### http 设置是这样的
    git config --global http.proxy http://127.0.0.1:6789
    git config --global https.proxy https://127.0.0.1:6789

#### ss或ssr 设置是这样的
    git config --global http.proxy 'socks5://127.0.0.1:6789'
    git config --global https.proxy 'socks5://127.0.0.1:6789'

*注意，得确认自己的代理使用的是哪个协议，哪个端口，比如我的就是 socks5 和 6789*

#### 重置代理
    git config --global --unset http.proxy
    git config --global --unset https.proxy

### SSL加速
在用户文件夹下，`vim /.ssh/config`，输入以下内容。`id_rsa` 文件需要换成你的。

    Host github.com
    User git
    Port 443
    Hostname ssh.github.com
    IdentityFile ~\.ssh\id_rsa
    TCPKeepAlive yes
    ProxyCommand connect -S 127.0.0.1:6789 %h %p

如果出现如下报错，应该是`SOCKS5`的原因
    
    FATAL: Cannot get password for user:xxx

此时新增一条环境变量即可：`SOCKS5_PASSWD`，值是你设置的密码，如果没有密码，随便填即可。