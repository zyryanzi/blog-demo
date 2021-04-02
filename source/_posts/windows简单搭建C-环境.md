---
title: windows简单搭建C++环境
date: 2021-03-17 16:16:15
tags:
    - Windows
    - C++
    - VSCode

categories:
    - 计算机科学
    - C++
---

*今天瞄了瞄QGis的源码，似乎是C++，那么在 Windows 下面如何尝试呢？首先得有个环境吧，于是搜了搜，由简入繁吧*
<!--more-->

### IDE
#### 下载 VSCode
接触一个新东西，首先从最简单的开始吧，据说用 VSCode 很方便，到这里下载就可以了

    https://code.visualstudio.com/Download
{% asset_img download.jpg 下载 %}

#### 安装
路径记得修改一下，之后一路默认下来，基本没什么问题。

#### 插件
喜欢中文的，安装个中文插件，如果无所谓，就只安装个c/c++的插件就可以了
{% asset_img plugins.jpg 插件 %}
在对话框中输入需要的插件名即可，然后在 `3` 处安装或卸载

### 编译环境
有了编辑器，接下来准备编译环境，可以考虑使用 `TDM-GCC`

#### 下载 
    https://jmeubank.github.io/tdm-gcc/download/
{% asset_img tdmgcc.jpg TDM-GCC %}
下载红框中这个即可

#### 安装
安装过程默认即可，最好修改一下路径，并牢记

#### 环境变量
最后配置环境变量

    我的电脑 -> 右键属性 -> 左侧高级系统设置 -> 下方环境变量按钮

在系统变量中找出 Path 条目并双击，在弹出的窗口中，即可加入环境变量
{% asset_img env.jpg 环境变量（也许已经存在） %}

#### 测试
重启 命令行工具，输入

    gcc -v

**至此，环境搭建完毕，接下来即可尝试 C++**