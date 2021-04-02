---
title: diskpart 的使用
date: 2021-03-25 17:46:55
tags:
    - Windows
    - Diskpart

categories:
    - 计算机科学
    - 小技巧
---
*在没有 U 盘的情况下，这种方法创建一个小引导盘，还是很便捷的。ps：需要搭配 RoadkilsDiskImage*
<!--more-->

使用管理员身份运行命令行工具，

    diskpart # 进入diskpart
    create vdisk file=F:\filename.vhd maximum=50 type=expandable  # 在指定路径创建名称为 filename.vhd 的虚拟盘文件，大小50M
    attach vdisk  # 挂载
    create partition primary #创建分区

然后就可以对其进行操作了，win + r 输入 diskmgmt.msc 即可查看相关信息