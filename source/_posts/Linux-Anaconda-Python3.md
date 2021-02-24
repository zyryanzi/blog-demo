---
title: Linux Anaconda Python3
date: 2021-02-23 15:06:01
tags:
    - Anaconda
    - Python2
    - Python3
categories: 
    - 计算机科学
    - Python
---

*请勿使用 root 用户直接安装*
<!--more-->
### 下载 Anaconda
    wget -c -t 20 https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh
{% asset_img download_anaconda.jpg download %}

### 开始安装 Anaconda
    ./Anaconda3-2020.02-Linux-x86_64.sh
执行以上命令后，会提示阅读许可文件，一路回车
{% asset_img license.jpg preinstall %}
直到提示输入 "yes"，敲回车，之后再敲一次回车
{% asset_img preinstall.jpg preinstall %}

### 确认安装
确认安装 anaconda
{% asset_img confirm_install.jpg confirm install %}

*至此安装过程完成。*

### 环境变量
    vim ~/.bashrc

#### 注释掉 “#>>> conda initiallize >>>” 之间的内容，并添加以下两行代码
    export PATH="/home/[username]/anaconda3/bin:PATH"
    export LD_LIBRARY_PATH="/home/[username]/anaconda3/lib:LD_LIBRARY_PATH"

#### 更新环境变量，执行以下命令
    source ~/.bashrc

如果出现问题，执行以下命令还原回来：

    export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

### 检验是否成功
    conda --version
{% asset_img check.jpg 检验 %}

### 怪异的(base) 与 activate、deactivate
注意到上边图片中命令行前端多了个 “(base)”，深感疑惑。
*原来这是conda的虚拟环境*
输入 python，如下图，是新安装的 python3
{% asset_img check_py3.jpg 检验python版本 %}

输入 conda deactivate，如下图，“(base)”消失了，
再输入python，变回了原来系统默认安装的 python2
{% asset_img check_py2.jpg 检验python版本 %}

但是重新打开命令行，又出现了“(base)”，因为 conda 配置中的 auto_activate_base 默认是 true
{% asset_img conda_conf_show.jpg show conda config %}

将其改为false，执行以下命令：

    conda config --set auto_activate_base false

如此需要开启 conda 环境时，只需执行以下命令即可：

    conda activate

至此可以开始使用 anaconda 了。


