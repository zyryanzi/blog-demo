---
title: centos7-certbot-python2-版本坑
date: 2021-02-03 10:46:17
tags: 
    - Linux
    - python2
category: 
    - 计算机科学
        - 系统
---

*centos7 系统，默认装的 python2，后来又装了python3*
*使用了 pip，默认包都是给python3用的，python2 的包一直没有更新*
*在使用 Let's Ecrypt 生成SSL证书时，遇到了版本不兼容的坑*
<!--more-->
首先 yum install certbot, yum 源中没有certbot，执行如下命令：

    yum --enablerepo=extras install epel-release

提示没有命令： yum-config-manager。 百度搜到要执行以下命令先：

    yum -y install yum-utils

然后更新 yum 

    yum -y upgrade

之后安装 certbot-nginx (听别人说要用这个)

    yum install certbot-nginx

以为到这里万事大吉，结果在执行以下命令时：

certbot certonly --webroot -w /usr/local/nginx/html/ -d sub.domain.com -m xxxxxx@xxx.com --agree-tos

报错：
    ......
    from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
    ......

搜索了好多，结果就是版本不匹配的问题，然后就各种升级，因为pip默认使用了python3的包路径，而certbot使用的python2，所以刚开始用 pip 对 pyOpenSSL, cffi, cryptography 等用到的包升级，都没有发生影响。后来别人告诉我要使用这样的命令：

    python -m pip ......

于是我执行了：
    
    python -m pip install --upgrade cryptography

结果告诉我不能升级，什么报错忘记了，然后执行了这条：

    python -m pip install --upgrade pyOpenSSL --ignore-installed pyOpenSSL

结果其他几个用到的包也一并升级了。

最后还遇到了一个小坑，更新了一下：

    python -m pip install --update urllib3

**总结：**
**在填坑过程中，一定要仔细查看错误日志，很多细节的地方其实已经明示暗示多次，否则白白浪费时间**



