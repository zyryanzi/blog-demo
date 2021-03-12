---
title: jenkins 自动化部署 shell脚本小试牛刀
date: 2021-03-10 15:53:53
tags:
    - jenkins
    - 构建
    - shell
categories:
    - 计算机科学
    - java
---

*前两天刚刚试了试 jenkins 服务的部署，现在来学习一下 shell 的编写。*
*对于外行人来说真是难啊，基础太差，什么都要学！*

### jekins 任务新建
在新建任务页面，输入任务名称，并选择`构建一个自由风格的软件项目`，然后点确定，

#### 源码管理
在接下来的页面中，配置源码路径，将需要部署的服务代码的git地址填入，例如

    https://github.com/yourname/programname.git

并且指定分支，如 */master
#### 构建触发器
{% asset_img build_trigger.jpg %}
此处`H/15 * * * *`中的`15`表示15分钟，自动检查git代码，有更新则自动构建

#### 构建环境
通常选择第一和第三项，构建之前清空工作空间，在日志中加入时间戳

#### 构建-shell脚本
注意Execute Shell的命令的执行路径需要写绝对路径或在Jenkins中为该命令设置环境变量。
pylint不能用虚拟环境的pylint，否则可能报错。
对于路径问题，可以通过which virtualenv来找到virtualenv在持续集成服务器具体的路径，然后在Jenkins设置环境变量，
设置路径是Jenkins主页=>系统管理=>系统设置=>全局属性

#### sh 获取返回值
##### 获取标准输出
**第一种**
    result = sh returnStdout: true ,script: "<shell command>"
    result = result.trim()
**第二种**
    result = sh(script: "<shell command>", returnStdout: true).trim()
**第三种**
    sh "<shell command> > commandResult"
    result = readFile('commandResult').trim()

##### 获取执行状态
**第一种**
    result = sh returnStatus: true ,script: "<shell command>"
    result = result.trim()
**第二种**
    result = sh(script: "<shell command>", returnStatus: true).trim()
**第三种**
    sh '<shell command>; echo $? > status'
    def r = readFile('status').trim()

##### 无需返回值，仅执行shell命令
    sh '<shell command>'

*原文：https://blog.csdn.net/zimou5581/article/details/94016158*

export JAVA_HOME=/opt/jdk1.8.0_101

cd  /root/usr/tomcat/apache-tomcat-7.0.90/uwin.versionrecord.service

mvn package

cd  /root/usr/tomcat/apache-tomcat-7.0.90/uwin.versionrecord.service/target

cp  uwin.versionrecord.service.jar  /root/usr/tomcat/apache-tomcat-7.0.90/webapps

sh  /root/usr/tomcat/apache-tomcat-7.0.90/jenkins.sh start

sh /root/usr/tomcat/apache-tomcat-7.0.90/bin/startup.sh

注意：
mvn package是打jar包的关键步骤，需要在你的linux服务器上安装配置好maven
jenkins.sh是自定义的jar包启动脚本，然后我将它放在/root/usr/tomcat/apache-tomcat-7.0.90里（放置位置可任选）
jenkins.sh

#!/bin/bash

#这里可替换为你自己的执行程序，其他代码无需更改

APP_NAME=uwin.versionrecord.service.jar

 

#使用说明，用来提示输入参数

usage() {
    echo "Usage: sh 执行脚本.sh [start|stop|restart|status]"

    exit 1

}

 

#检查程序是否在运行

is_exist(){
  pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}' `

  #如果不存在返回1，存在返回0    

  if [ -z "${pid}" ]; then

   return 1

  else

    return 0

  fi

}

 

#启动方法

start(){
  is_exist

  if [ $? -eq "0" ]; then

    echo "${APP_NAME} is already running. pid=${pid} ."

  else

    nohup java -jar $APP_NAME > /dev/null 2>&1 &

    echo "${APP_NAME} is  running"

  fi

}

 

#停止方法

stop(){
  is_exist

  if [ $? -eq "0" ]; then

    kill -9 $pid

  else

    echo "${APP_NAME} is not running"

  fi 

}

 

#输出运行状态

status(){
  is_exist

  if [ $? -eq "0" ]; then

    echo "${APP_NAME} is running. Pid is ${pid}"

  else

    echo "${APP_NAME} is NOT running."

  fi

}

 

#重启

restart(){
  stop

  start

}

 

#根据输入参数，选择执行对应方法，不输入则执行使用说明

case "$1" in

  "start")

    start

    ;;

  "stop")

    stop

    ;;

  "status")

    status

    ;;

  "restart")

    restart

    ;;

  *)

    usage

    ;;

Esac

配置信息填写完整后点击保

2.4 构建工程


选择立即构建，可在Build History下看到此次构建，蓝色小球代表构建成功，红色小球代表构建失败。