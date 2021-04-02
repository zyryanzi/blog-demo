---
title: Windows环境下使用QGIS - 环境构建及编译
date: 2021-03-19 10:41:34
tags:
    - QGIS
    - Microsoft Visual Studio

categories:
    - 计算机科学
    - GIS
---

*据说下雨天和巧克力很配，我觉得咖啡也不错*
*至于 Geoserver 那我还是选择 QGIS，毕竟开源才是乘以，白嫖才是王道。*
*因为这个是用C++写的，而我对C++几乎是一窍不通，所以就按照文档做下来，困难重重。*
<!--more-->
**说明：**
    "[OSGEO4W_ROOT]" 表示 `OSGeo4W` 安装的目录
    "[QGIS_SOURCE]" 表示 `QGIS` 源码根目录

### 安装并配置 VS
这个网址是 github 中的文档，第 4 点是Windows环境下的构建
https://github.com/qgis/QGIS/blob/master/INSTALL.md#412-other-tools-and-dependencies
这里提到要使用 `Visual Studio 2015 Community Edition` , 其实就是 `Microsoft Visual Studio 14.0`，

VS 版本对照

|名字|版本号|简称|全称|
|----|----|----|----|
|msvc70|VC7.0|VS2002|Microsoft Visual Studio 2002|
|msvc71|VC7.1|VS2003|Microsoft Visual Studio 2003|
|msvc80|VC8.0|VS2005|Microsoft Visual Studio 2005|
|msvc90|VC9.0|VS2008|Microsoft Visual Studio 2008|
||VC10.0|VS2010|Microsoft Visual Studio 2010|
||VC11.0|VS2012|Microsoft Visual Studio 2012|
||VC12.0|VS2013|Microsoft Visual Studio 2013|
||VC13.0|VS2014|Microsoft Visual Studio 2014|
||VC14.0|VS2015|Microsoft Visual Studio 2015|

VS的版本是值得注意的，否则在执行`OSGeo4W-dev.bat`的时候，可能会遇到各种各样的报错，这些报错多数在这个文件中发生`~\QGIS\ms-windows\osgeo4w\msvc-env.bat`。
从其代码中发现，是对应到这个版本的 `Microsoft Visual Studio 14.0`，那么只要安装这个版本的 VS 就可以了。
在指南中明确写到，选择自定义安装，并且选上以下两个

    "Common Tools for Visual C++ 2015" under "Visual C++"
    "Tools (1.4.1) and Windows 10 SDK (10.0.14393)" under "Universal Windows App Development Tools".

因为我的毛毛躁躁，直接一路点下来，根本没细看，导致缺少文件，不过也有解决办法：
如果是使用默认项安装完，可能会没有这个文件:

    ~\Microsoft Visual Studio 14.0\VC\vcvarsall.bat

此时需要打开 VS 新建一个项目，选择 Visual C++, 并安装右侧红框中的其中一个，完成后，就可以了。

### 其他工具和依赖
[CMake](https://cmake.org/files/v3.12/cmake-3.12.3-win64-x64.msi)
GNU flex, GNU bison and GIT with cygwin [32bit](https://cygwin.com/setup-x86.exe) or [64bit](https://cygwin.com/setup-x86_64.exe)
OSGeo4W [32bit](https://download.osgeo.org/osgeo4w/osgeo4w-setup-x86.exe) or [64bit](https://download.osgeo.org/osgeo4w/osgeo4w-setup-x86_64.exe)
[ninja](https://github.com/ninja-build/ninja/releases/download/v1.7.2/ninja-win.zip)

以上这几个工具都是需要安装的。

- 对于cygwin，安装时需注意以下几个都要安装:
bison
flex
git (即使你有git，也要再装一遍)

- 对于 OSGeo4W (选择第三项：Advanced Install)，默认是全部跳过，我们需要安装的只有下边这个:
qgis-dev-deps
不过在安装的过程中，还是会自动带上依赖包。
**注意**
在 OSGeo4W 中不要安装额外的包，否则可能产生冲突
*Tips:* OSGeo4W 由于是在线下载安装，可以对列表中的服务器域名逐个 ping 一下，选择延迟最低的。

值得注意的是，对于OSGeo4W，如果改变了安装路径，记得在环境变量中加入对应的配置 `OSGEO4W_ROOT`，
还有 `VCSDK`的版本号，也需要注意一下

- ninja
只需要解压，并将 `ninja.exe` 拷贝至 `[OSGEO4W_ROOT]\bin\`

### 下载源码
*cygwin 进入其他驱动盘的命令 cd d:/*
启动 `Cygwin64 Terminal` 并进入一个让你舒适的路径，并满怀敬意的执行以下命令：

    git clone https://github.com/qgis/QGIS.git

然后进入 `QGIS` 并关闭库文件权限

    cd QGIS
    git config --global core.filemode false

创建 `[OSGEO4W_ROOT]\OSGeo4W-dev.bat`，并在其中写入如下代码：

    @echo off
    call [QGIS_SOURCE]\ms-windows\osgeo4w\msvc-env.bat x86_64
    @cmd

保存并执行（双击就行）

-----------------------------------------------------------未完成