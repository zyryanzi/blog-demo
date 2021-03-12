---
title: jenkins Linux环境服务搭建
date: 2021-03-10 09:55:31
tags:
    - jenkins
    - Linux
categories: 
    - 计算机科学
    - java
---

**写在前面**： *jenkins是个java服务，对资源消耗比较大，自己搞东西，随便练练手就好，资源不够，随时被kill*
<!--more-->

### 环境准备及部署
jenkins 依赖和关联的东西比较多，我熟悉的也就是 java, git, maven, tomcat 这些。

#### jdk
首先用 java –version 进行测试，看是否已安装了java
如果没有再进行安装，jdk 的安装可以用 yum, 也可下tar包自己安装

如果自己下载安装，完事需要配置环境变量

    vim /etc/profile

加入

    export JAVA_HOME=/opt/jdk1.8.0_101（tar包解压路径）
    export PATH=$JAVA_HOME/bin:$PATH
    export CLASSPATH=.:$JAVA_HOME/lib/tools.jar

生效配置文件

    source /etc/profile

#### git
代码托管和版本控制现在很少用SVN了，github和gitlab居多，我见到的是这样，git也是要安装一下的
首先看下是否已经安装

    git --version

如果有输出，则代表已安装，如果没有安装，则可以使用yum等工具安装，或者下载源码安装

##### git 安装
解压源码 tar 包，并进入，执行以下命令
    
    make prefix=/usr/local/git all
    make prefix=/usr/local/git install

如果 make 时报错: "curl/curl.h：没有那个文件或目录"
则需要安装curl：

    wget https://curl.haxx.se/download/curl-7.51.0.tar.gz
    tar -zvxf curl-7.51.0.tar.gz
    cd curl-7.51.0/
    ./configure -prefix=/usr/local/curl
    make
    make install
    export PATH=$PATH:/usr/local/curl/bin
    
    cp -r curl-7.51.0/include/curl/ /usr/include/

##### git 配置环境变量
查看 git 位置
    
    whereis git

在结尾加入

    vim /etc/profile
    export GIT_HOME=/usr/local/git
    export PATH=$PATH:$GIT_HOME:$PATH

生效配置文件

    source /etc/profile

#### maven
如果发布java项目，依赖包的管理是必不可少的，maven我们已经很熟悉了，这里需要安装一下
下载maven tar包进行解压，并进入
创建文件夹
    mkdir maven_lib

修改 maven 仓库配置文件

    vim [maven_home]/conf/settings.xml

找到 `<localRepository>` 标签，改为 

    <localRepository>/usr/local/apache-maven-3.6.3/maven_lib</localRepository>

配置镜像，找到 `<mirrors>` 标签，添加下级元素

    <mirrors>
        <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>

保存并推出。
配置环境变量
    
    vim /etc/profile

加入

    export M3_HOME=/root/usr/tomcat/apache-maven-3.6.3/bin
    export PATH=$JAVA_HOME/bin:$M3_HOME/bin:$PATH

生效配置文件
    
    source /etc/profile

#### tomcat
##### tomcat 安装
下载 tar 包，如：apache-tomcat-7.0.90.tar.gz 并解压缩
##### tomcat 配置
修改端口，修改tomcat里面conf/server.xml，server.xml文件中有三个端口设置

    <Server port="8005" shutdown="SHUTDOWN"> # 关闭时使用
    <Connector port="8080" protocol ="HTTP/1.1"  connectionTimeout="20000" redirectPort="8443" /> # 一般应用使用
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" /> # 为AJP端口，即容器使用，如 APACHE 能通过 AJP 协议访问 Tomcat 的 8009 端口

可把这三个修改成你需要的端口号（本次端口设置为默认的端口）
修改完成后按ESC键，再按shift键，在：后填入wq 进行保存退出
命令行输入 source server.xml即可生效

#### jenkins
##### jenkins 部署
jenkins 是一个 java 服务，需要放在容器中来启动，这里选择使用tomcat作为容器

从官网下载jenkins的war包，地址https://jenkins.io/download/
将jenkins.war包放在 tomcat 根目录下面的webapps里。
启动tomcat。tomcat会解压war包，生成一个jenkins文件夹，

**而且会在root目录下生成一个.jenkins的文件夹。**
启动之后，访问 IP:端口号，测试一下tomcat是否正常启动。
在浏览器上访问（http://ip:端口/jenkins）即可访问 jenkins

##### jenkins 初次访问
首次进入jenkins如要输入密码，密码位置：

    /root/.jenkins/secrets/initialAdminPassword

正常情况下，进入选择插件安装界面，选择第一个（Install suggested plugins）
离线情况下，输入密码点击继续，然后选择跳过插件安装（本次实验的情况：可以在安装完成后手动上传插件）
安装完成之后，需要创建第一个用户
创建完成之后就可以正常使用jenkins了。

#### jekins 使用前准备
##### 一些异常或报错
起初在修改配置项的时候，服务器的nginx一直报出这样一条错误：

    SSL: error:140E0197:SSL routines:SSL_shutdown:shutdown while in init

翻阅了一些前人的经验，发现是 nginx 和 openSSL 的版本太低，这个bug在 nginx-1.10.3 以后修复了，而 openSSL 要升级到j版本以后
解决上述问题后，又出现了另外一个问题，jenkins 报出了这样一条错误

    hudson.security.csrf.CrumbFilter.doFilter Found invalid crumb xxxxxx

这个错误和jenkins的一条配置有关，在全局安全配置中，有一处`跨站请求伪造保护`，将下方启用代理兼容勾选即可。
{% asset_img crumb.jpg 跨站请求伪造保护配置 %}

##### 插件
jekins 的插件很丰富，可以访问这里查看下载：http://updates.jenkins-ci.org/download/plugins/
下载插件到本地后，进入插件管理 -> 高级，进行选择上传，如下图
{% asset_img plugin.jpg 插件管理 %}

##### 全局工具
以上 jdk, maven 等工具也要在 `系统管理->全局工具配置` 进行一下路径配置，如下图所示
{% asset_img tool_jdk.jpg jdk 配置 %}
{% asset_img tool_git.jpg git 配置 %}
{% asset_img tool_maven.jpg maven 配置 %}

### Tips
下载可以使用 wget [url]
查找程序位置可以使用 whereis [name] 或 find [path] -name [name]
tar包解压可以使用 tar -zxvf [filename]