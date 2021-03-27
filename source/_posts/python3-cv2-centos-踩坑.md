---
title: python3-cv2-centos-踩坑
date: 2021-03-11 10:25:54
tags:
    - CentOS
    - Python3
    - OpenCV
categories:
    - Linux
    - Python
---

*最近读别人的文章发现了一个有趣的人脸识别，附上链接：*
*http://xublog.wang/2020/11/03/opencv-flask-web%E4%BA%BA%E8%84%B8%E8%AF%86%E5%88%AB/*
*然而在 Win10 上面测试没什么问题，到 CentOS 上面就步履维艰了，记录一下爬坑的过程*

<!--more-->
### 原始操作
起初报了这样的错误
{% asset_img cv2_import.jpg cv2 导入失败 %}
看起来应该是导入cv2依赖包失败了。这时我想起了一条命令

    pip freeze > requirements.txt

这样可以将本地依赖原本的导出一个文件，再在另一台机器上执行下述命令，下载相同的依赖

    pip install -r requirements.txt

ps 此处也许需要用到 python3 -m pip install... 为python3 安装

然而这并没有解决我得问题，依然报同样的错误。也许是因为我从 win10 导出依赖，不完全适用于 CentOS。

### 进阶操作
在各种寻找的尝试过程中，发现了有人提到了这个包 opencv-contrib-python，于是我试了试下面的命令

    pip install -y opencv-contrib-python

控制台的报错信息有了变化，变成了这样
{% asset_img cv2_libgl.jpg libGL.so.1 %}
这一条信息，关系到了另外一条依赖

    yum install mesa-libGL

以为这条结束后，就能万事大吉了，因为看起来真的很相关，没想到这只是一道开胃小菜。

### 高级操作
顺利完成了依赖安装，报错依然存在，仍然缺少依赖，解法遍寻不得，却看到了不少 Ubuntu 用户的解决方案

    pip3 install opencv-contrib-python==3.3.0.9 -i https://www.piwheels.org/simple # 安装3309版本
    sudo apt-get update #安装依赖库
    sudo apt-get install libhdf5-dev
    sudo apt-get install libatlas-base-dev
    sudo apt-get install libjasper-dev
    sudo apt-get install libqt4-test
    sudo apt-get install libqtgui4

我滴个乖乖，这么多包啊，关键是 yum 里面并没有这些包啊！于是我就一个个的找 yum 中的对应包，执行了如下几条命令

    yum -y install hdf5-devel
    yum -y install liblas-devel atlas-devel
    yum -y install libjasper-devel

终于，可以正常运行了

仅做此记录，希望您可以不用再去一个个的找了。
