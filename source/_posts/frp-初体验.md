---
title: frp-初体验
date: 2021-02-25 09:53:12
tags:
---

*都听人说黑群晖，自己也搭一个试试（挺麻烦，过程以后再写），虽然一直开着费电，不过练习一下还是蛮有趣。*
*搭好之后，用一个免费的局域网软件和自己的手机连上了，但是很不稳定，于是研究下 frp，看看能不能取得更好的效果*
<!--more-->
### 服务端安装配置
#### 服务器上下载最新release的 frp
首先有台有外网（外部网络）IP的服务器，我的是Centos7系统的，进入 /usr/local 目录
（我也不知道为啥进这里，因为别人都进这）

    wget https://github.com/fatedier/frp/releases/download/v0.35.1/frp_0.35.1_linux_amd64.tar.gz
{% asset_img download.jpg 下载 %}

#### 解压文件并进入之
    tar -vxf frp_0.35.1_linux_amd64.tar.gz
    cd frp_0.35.1_linux_amd64
{% asset_img tar.jpg 解压 %}

#### 编辑 frps.ini
    vim frps.ini
{% asset_img frpsini.jpg 编辑 frps.ini %}

    [common]
    bind_port = 7000            # frp在服务端机器的端口
    vhost_http_port = 10101     # frp监听外网http请求的端口
    dashboard_port = 7003       # frp控制台端口
    dashboard_user = admin      # frp控制台用户名
    dashboard_pwd = admin       # frp控制台访问密码
    [web]                       # 代理名称，与客户端设置一致
    listen_port = 6000          # 服务端配置客户端 代理的端口（好绕）
    auth_token = auth_code      # 我没用上

vhost_http_port 原来是80，我在启动的时候，报冲突，于是改了一个，记住就好，待会要配到frpc.ini；
auth_token 自己改下就行我还不知道哪里用到，回头再补充；

#### 配置 frp.service
    vim /usr/lib/systemd/system/frp.service
{% asset_img frp.service.jpg frp.service %}
    [Unit]
    Description=The nginx HTTP and reverse proxy server
    After=network.target remote-fs.target nss-lookup.target

    [Service]
    Type=simple
    ExecStart=/usr/local/your_folder_name/frps -c /usr/local/your_folder_name/frps.ini
    KillSignal=SIGQUIT
    TimeoutStopSec=5
    KillMode=process
    PrivateTmp=true
    StandardOutput=syslog
    StandardError=inherit

    [Install]
    WantedBy=multi-user.target

将your_folder_name更换为自己frp文件夹

#### 启动服务端
    systemctl start frp
    systemctl status frp
{% asset_img frps_suc.jpg 成功启动 %}

### 客户端安装配置
#### 客户端下载
因为我的系统是win10, 于是下载了这个
{% asset_img win_download.jpg win下载 %}

#### 将其解压到一个你喜欢的地方并进入之
{% asset_img win_frp.jpg win解压 %}

#### 编辑 frpc.ini
{% asset_img win_frpcini.jpg 编辑win_frpc.ini %}

    [common]
    server_addr = 10.10.10.10       # 外部服务器 IP
    server_port = 7000              # frp 服务的端口
    [web]                           # 和上面的配置一样名称
    type = http                     # 这里改成 http（不明所以）
    local_ip = 127.0.0.1            # 本机 IP
    local_port = 8080               # 内网要暴露的服务端口
    remote_port = 10101             # http 访问的端口
    custom_domains = 10.10.10.10    # 外部网IP或域名

server_addr 就是你的服务端外网IP
type 改为http
custom_domains 可以填入外部网ip

#### 启动
完成编辑，就可以启动了，进入frp的根目录，执行：
    frpc.exe
第一次没有成功，因为忘记在服务器上开放7000端口。
*开放端口请参考另一篇文章：https://nontech.top/2021/02/01/firewalld-%E9%98%B2%E7%81%AB%E5%A2%99-%E6%90%AC%E8%BF%90/*

再次执行命令，成功启动了。

至此完成，后续补充~~~
访问：10.10.10.10:10101 即可访问到内网服务
