---
title: centos7 v2ray 一键安装
date: 2021-02-13 16:48:26
tags: 计算机科学
    网络技术
---

搞个便宜 vps，研究一下 v2ray，发现大家都在一键搞定，所以也来膜拜一下大神。
<!--more-->
## 安装准备
1.准备好curl;

    yum update -y && yum install curl -y

## 开始安装
    bash <(curl -s -L https://git.io/v2ray.sh)

然后选择 '1' 回车，可以都使用默认设置，默认TCP传输协议，端口也可使用默认的；
如果需要shadowsocks，要选择 'Y'，然后配置端口，密码，加密协议，可以全部默认；
回车，等待安装完成。

可以直接复制粘贴二维码链接，扫码节省配置步骤

## 管理 V2RAY
    v2ray info 查看 V2Ray 配置信息
    v2ray config 修改 V2Ray 配置
    v2ray link 生成 V2Ray 配置文件链接
    v2ray infolink 生成 V2Ray 配置信息链接
    v2ray qr 生成 V2Ray 配置二维码链接
    v2ray ss 修改 Shadowsocks 配置
    v2ray ssinfo 查看 Shadowsocks 配置信息
    v2ray ssqr 生成 Shadowsocks 配置二维码链接
    v2ray status 查看 V2Ray 运行状态
    v2ray start 启动 V2Ray
    v2ray stop 停止 V2Ray
    v2ray restart 重启 V2Ray
    v2ray log 查看 V2Ray 运行日志
    v2ray update 更新 V2Ray
    v2ray update.sh 更新 V2Ray 管理脚本
    v2ray uninstall 卸载 V2Ray

### Tips: 
~ 请记得查看防火墙是否开启，并开放相应端口
~ 记得调整服务器时间与本地时间相同，否则 v2ray 校验不通过

*参考链接：https://x.usbfu.com/%E5%AE%89%E8%A3%85-v2ray/*


