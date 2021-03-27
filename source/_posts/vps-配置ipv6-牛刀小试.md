---
title: vps-配置ipv6-牛刀小试
date: 2021-03-04 12:40:45
tags:
    - vps
    - ipv6
    - tunnel
categories:
    - 计算机科学
    - 网络技术
---

*使用vps有一段时间了，梯子一直稳稳地。然而最近一件事彻底改变了这个现状。*
*帝都召开宇宙神秘大会，ipv4都瓦特了，然而队友的ipv6依然坚挺。*
*眼热之际，也使我开启了ipv6配置之旅*
<!--more-->
### 初识 tunnelbroker
请教了搜索引擎网站，发现多数大佬都提到了 tunnel，而且比较推荐 tunnelbroker.net。
于是我也来到了这里，注册账户之后，要经过邮箱验证一下，顺便在首页还回答了几个问题，蛮有趣，及格分。

点击左侧的`Create Regular Tunnel`，进入创建页面
{% asset_img index.jpg 首页 %}
填入你的ipv4，选择合适的 tunnel server 地址
{% asset_img create.jpg 创建页面 %}
拉到最下面点击`submit tunnel`
成功之后就会跳转到成功页面，红框里面是关键的信息
{% asset_img succ.jpg 创建成功 %}

至此，tunnel 创建成功，不过不要急着离开，点击`Example Configurations`，并选择`Linux-net-tools`（如果是乌班图，选择`Debian/Ubuntu`），
复制文本框中生成的内容，并ssh到你的服务器中运行，
{% asset_img code_copy.jpg 执行命令 %}

### ipv6 开机启动
1. 创建文件

    `vim /root/ipv6.sh`

2. 在其中写入以下内容：

    `#!/bin/bash`

    `ifconfig sit0 up`
    `ifconfig sit0 inet6 tunnel ::YOUR-IPV4`
    `ifconfig sit1 up`
    `ifconfig sit1 inet6 add YOUR-IPV6`
    `route -A inet6 add ::/0 dev sit1`

其实就是刚刚`Example Configurations`中复制过来的内容，加上首行`#!/bin/bash`

3. 增加权限
    
    `chmod +x /root/ipv6.sh`

4. 重启自启

    `vim /etc/rc.d/rc.local`

    在最后一行加入

    `sh /root/ipv6.sh`

**以上就是本次尝试，希望能给大家带来启发和帮助**