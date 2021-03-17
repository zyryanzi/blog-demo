---
title: Windows-Nexus 自启动
date: 2021-03-16 13:12:43
tags:
    - Windows
    - 批处理
    - 自启动
categories:
    - 计算机科学
    - Windows
---

*本地 PC 搭建了 Nexus, 为了让日常变得简单，为其添加了一条批处理，使其自启动，于此记录*
<!--more-->

### 创建 startup.bat
在 `nexus.exe` 同级目录，创建一个 `.bat` 结尾的文件，如 startup.bat，并使用编辑器打开，想其中复制以下代码：

    @echo off
    if "%1" == "h" goto begin 
    mshta vbscript:createobject("wscript.shell").run("%~nx0 h",0)(window.close)&&exit 
    :begin 
    set _task=nexus     % 此处 nexus 为 要启动的可执行文件 去掉 .exe %

    set _runexe=start.vbs

    :checkstart
    echo "检查进程"
    for /f "tokens=5" %%c in ('qprocess.exe ^| find "%_task%"') do ( if %%c==" " ( @echo ) else goto checkag  )
    goto startsvr

    :startsvr

    echo ******开始启动程序*******

    echo 启动时间 %time%  >> restart.log    % 此处将启动时间写入日志，可以不做保留 %

    echo set ws=wscript.createobject("wscript.shell") >> %_runexe%
    echo ws.run "nexus.exe /run",0,false >> %_runexe%   % “nexus.exe /run” 为启动命令%

    start /b %_runexe%


    echo Wscript.Sleep Wscript.Arguments(0) >%tmp%\delay.vbs

    cscript //b //nologo %tmp%\delay.vbs 10000

    del %_runexe% /Q

    echo *******程序启动完成********

    goto checkstart


    :checkag

    echo Wscript.Sleep Wscript.Arguments(0) >%tmp%\delay.vbs

    cscript //b //nologo %tmp%\delay.vbs 10000

    goto checkstart

### 打开自启动路径
`win + r` 打开 `运行` 输入 `shell:Common Startup`，
将上一步的 startup.bat 创建快捷方式，并将快捷方式考进 `启动` 目录。

*启动 nexus，此后每次关机或重启，都会自动启动nexus，省去很多麻烦。
