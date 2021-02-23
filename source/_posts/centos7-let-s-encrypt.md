---
title: centos7 let's encrypt
date: 2021-02-02 11:17:21
tags: 
    - Linux
    - https
    - ssl证书
category: 
    - 计算机科学
        - 系统
---
*转载于：https://my.oschina.net/u/174201/blog/1577784*
**感谢大神**
<!--more-->

## Let's Encrypt
如果要启用 HTTPS，我们需要一个 CA 证书，Let's Encrypt 是一个免费的证书颁发机构，由 ISRG（Internet Security Research Group）运作。

## 使用 Certbot 获取证书
Certbot 是 Let's Encrypt 官方推荐的证书获取工具，它可以帮助很方便的获取和更新 Let's Encrypt 证书，Certbot 支持所有 Unix 内核的操作系统。

## 安装 Certbot 客户端
    yum install -y epel-release
    yum install -y certbot
注： 安装和执行 certbot 过程中，可能会报一些错误，这些错误主要是使用到的一些 python 库版本不匹配问题，根据错误提示更新安装相应版本的库即可。

*如果遇到问题，可以参考我的这篇文章：centos7-certbot-python2-版本坑*

## 获取证书
因为使用 Certbot 获取证书时，Let's Encrypt 服务器会访问 http://sub.domain.com/.well-known 来验证你的域名服务器，因此需要修改 nginx 配置文件，配置 .well-known 指向本地一个目录：

    server {
        ......
        location /.well-known {
            alias /usr/local/nginx/html/.well-known;
        }
        ......
    }

然后就可以使用 certbot 命令来获取证书了，获取证书时需要输入你的Email并接受用户条款。需要注意：-w 指定的 web 目录需要和前边 nginx 配置的 .well-known 的本地目录一致（/usr/local/nginx/html）：

    certbot certonly --webroot -w /usr/local/nginx/html/ -d sub.domain.com -m xxxxxx@xxx.com --agree-tos
    -w 指定 webroot 目录
    -d domain 想要获取的证书域名，支持多个域名

但是有些时候我们的一些服务并没有根目录，例如一些微服务，这时候使用 --webroot 就走不通了。certbot 还有另外一种模式 --standalone，这种模式不需要指定网站根目录，他会自动启用服务器的443端口，来验证域名的归属。我们有其他服务（例如nginx）占用了443端口，就必须先停止这些服务，在证书生成完毕后，再启用。
    certbot certonly --standalone -d sub.domain.com -m xxxxxx@xxx.com --agree-tos

如果成功获取证书，你的密钥和证书存放在 /etc/letsencrypt/live/sub.domain.com/ 目录：

    ll /etc/letsencrypt/live/sub.domain.com/
    
    cert.pem -> ../../archive/sub.domain.com/cert1.pem
    chain.pem -> ../../archive/sub.domain.com/chain1.pem
    fullchain.pem -> ../../archive/sub.domain.com/fullchain1.pem
    privkey.pem -> ../../archive/sub.domain.com/privkey1.pem

|文件名|说明|
|---|---|
|cert.pem|	服务端证书|
|chain.pem|	浏览器需要的所有证书但不包括服务端证书，比如根证书和中间证书|
|fullchain.pem|	包括了cert.pem和chain.pem的内容|
|privkey.pem|	证书的私钥|

## 删除证书
有时需要删除已生成的证书，重新生成。可使用如下命令进行删除：

    certbot delete --cert-name sub.domain.com

## 生成 dhparam
nginx 配置 https 时，需要 dhparam，使用如下命令进行生成：

    openssl dhparam -out /etc/nginx/sites-enabled/dh4096.pem 4096

## 配置 HTTPS
### 配置 nginx
    server {
        listen 80;
        server_name sub.domain.com;
        rewrite ^ https://$server_name$request_uri? permanent;
    }
    
    server {
        listen 443 ssl;
    
        server_name sub.domain.com;
    
        include /etc/nginx/sites-enabled/sub.domain.com.ssl;
    
        location / { try_files $uri @proxy_to_app; }
        location @proxy_to_app {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-Forwarded-Proto https;
            proxy_redirect off;
            proxy_pass http://127.0.0.1:8080;
        }
    }

sub.domain.com.ssl 文件配置内容：

    ssl on;
    ssl_certificate /etc/letsencrypt/live/sub.domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/sub.domain.com/privkey.pem;
    ssl_prefer_server_ciphers on;
    ssl_dhparam /etc/nginx/sites-enabled/dhparam.pem;
    ssl_ciphers HIGH:!ADH:!MD5:!aNULL:!eNULL:!MEDIUM:!LOW:!EXP:!kEDH;
    ssl_protocols TLSv1.2 TLSv1.1 TLSv1;
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_stapling on;
    ssl_stapling_verify on;
    add_header Strict-Transport-Security max-age=15768000;

### 配置 tomcat

配置 tomcat 支持 https，有两处需要修改：
　～在 Connector 节点增加 proxyPort="443"

    <Connector port="8080" protocol="HTTP/1.1"
        connectionTimeout="20000"
        redirectPort="8443" proxyPort="443" />

　～添加如下 Value 节点

    <Valve className="org.apache.catalina.valves.RemoteIpValve"
        remoteIpHeader="x-forwarded-for"
        remoteIpProxiesHeader="x-forwarded-by"
        protocolHeader="x-forwarded-proto" />
    
### 测试
#### *如果开启了防火墙，记得开放 443 端口*
启动 nginx 或重新载入配置，使用浏览器打开 https://sub.domain.com/

    service nginx reload

可以使用在线工具 https://www.ssllabs.com/ssltest 来检测证书情况。

### 自动更新证书

Let's Encrypt 证书只有 90 天有效期，我们需要在证书到期之前更新证书，certbot 提供了相应的命令: certbot renew。

    /usr/bin/certbot renew --dry-run

可以将此更新命令添加到计划任务中，certbot renew 命令只会更新还有 30 天才会到期的证书，所以我们可以每隔 2 个月在凌晨3:30执行一次更新操作即可，创建一个新文件 certbot-auto-renew-cron，写入 cron 计划内容：

    30 3 * */2 * /usr/bin/certbot renew --post-hook "service nginx restart" --quiet >> /var/log/cerbot.log
--pre-hook 这个参数表示执行更新操作之前要做的事情
--post-hook 这个参数表示执行更新操作完成后要做的事情

### 启动 crontab 定时任务

    crontab certbot-auto-renew-cron