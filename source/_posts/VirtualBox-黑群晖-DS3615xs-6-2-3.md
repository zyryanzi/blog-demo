---
title: VirtualBox-黑群晖-DS3615xs-6.2.3
date: 2021-03-27 21:51:15
tags:
    - 黑群晖
    - DSM6.2.3
    - VirtualBox
    - DS3615xs

categories:
    - 计算机科学
    - NAS
---

*原来虚拟机上安装了 DS3615xs 的 DSM6.1.7，发现有些套件没法安装，例如 Moment, Drive 等等。于是努力的爬了许多坑，安装了这个 DSM6.2.3的系统*
*本文只针对有 VirtualBox 情怀的小伙伴，因为 VMware 相关的指引有很多，而且要更简单一些*
*PS Virtual Box 仅支持最大2T空间*

<!--more-->

### 1. 引导文件 synoboot.img
这个写在最前面，也是最重要的，一定要下 1.03b 的引导文件。下面的地址可能有点慢，也可能需要架梯子，但是肯定可以用。也可以尝试请搜索引擎帮忙。

    https://mega.nz/file/OV4gVKyZ#dCgfXx1bgAOyvbFwFiov3s7RSNoFuqnAcNmSllLoUiw

### 2. 网络设置，MAC地址
虚拟机中的网络选用桥接的方式，桥接到你当前使用的网卡即可；
控制芯片选择 Intel PRO/1000 MT 桌面；
*重点：点击高级，看到最下面有一栏 mac地址，这个一定要和上面引导文件中的一致，引导文件中是叫 mac1，如果你想设置多块网卡，在 mac1 下面继续添加 mac2 等等即可。*
#### 2.1 .img文件的挂载
目的是查看或修改 .img 文件中内容，推荐使用 OSFMount, 使用方法网上一搜一大堆，这里不再赘述，只是引导文件配置都在 15M 大小的那个分区里，名字叫 grub.cfg。
#### 2.2 U盘vid, pid
如果是使用U盘创建引导，这里还需要查验一下U盘的 vid 和 pid，然后将 .img 文件中的改为一致。

### 3. 引导文件虚拟化
这里尝试过很多办法，因为 6.1.7 的时候，使用的别人的 .vmdk 文件，这里就理所当然以为还是制作这个，直到见到一篇帖子，作者 renegadeBE 很明确的给出了可行方案

    https://xpenology.com/forum/topic/13834-virtualbox-dsm-62-ds3615xs-config/

这个版本使用 .vdi 格式, 并且在虚拟机中配置 SATA(AHCI))控制器 加载。
那么由 .img 转化为 .vdi 可以使用 VirtualBox 的内部命令：

    VBoxManage convertfromraw --format VDI [sourcefile] [targetfile]
    eg.
    VBoxManage convertfromraw --format VDI "F:\nas\synology\6.2.3\synoboot.img" "F:\nas\synology\6.2.3\synoboot032702.vdi"

这里要使用管理员身份打开命令行工具，并进入 VirtualBox 根目录。

### 4. 存储空间挂载
这个版本的存储空间我尝试了 SATA，但是不行，我又换成了 LsiLogic 就可以了。

### 5. CPU 芯片组
在虚拟机的系统设置栏目中，芯片组值得注意一下，作者写到需要选择 ICH9，其他均不成功。

### 6. PAT 文件
这个就是群晖的系统啦，DSM_DS3615xs_25426.pat 这一版就刚刚好，没必要一定要追求最新的，差不多出彩的功能都可以用。

    https://global.download.synology.com/download/DSM/release/6.2.3/25426/DSM_DS3615xs_25426.pat

**以上就是我爬了两天爬出来的坑，其他常规操作随时请教搜索引擎即可，最后附上我的虚拟机配置截图**
{% asset_img conf.jpg 配置截图 %}

