---
title: windows10-关闭自动更新摸索
date: 2021-03-03 11:04:07
tags:
    - windows
    - 小技巧
    - 自动更新
categories:
    - 计算机科学
    - windows
---

*天下苦秦久矣，苦windows更新亦久矣……*
<!--more-->

### 常规操作 在服务中禁用 Windows Update
#### 打开服务界面
    win + r -> services.msc
{% asset_img services.jpg 服务界面 %}

#### 禁用 Windows Update
1. 找到 Windows Update，右键属性，如下图
{% asset_img attribute.jpg 属性 %}
点击停止，并将启动类型改为禁用，随后点击恢复

2. 在“恢复”的卡片中我们能看到第一条选项就是我们要修改的。
{% asset_img prevent.jpg 恢复卡片 %}
将“重新启动服务”改为“无操作”。

#### 停用、禁用 Update Orchestrator Service 并修改注册表
1. 找到`Update Orchestrator Service(更新Orchestrator服务)`，右键属性，停用并禁用
{% asset_img orchestrator.jpg orchestrator %}

2. 修改注册表

    win + r -> regedit

找到并定位到`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\UsoSvc`，
{% asset_img regedit.jpg 注册表 %}

右键修改，将值改为`4`
{% asset_img start.jpg 注册表值 %}

继续在`Start`同级找到`FailureActions`，右键->修改二进制数据
{% asset_img FailureActions.jpg 注册表二进制 %}

从前到这里就可以了，不过现在我们还要修改一个地方。

### 升级操作 关闭 Windows Update Medic Service
1. 重新进入服务界面，同上，并找到 Windows Update Medic Service，
不过我没找到，但是找到了一个 `Windows 更新医生服务`，看起来字面意思差不多，我就当它是了
{% asset_img update_medic.jpg 更新医生服务 %}
还是这几个地方，进行和 Windows Update 类似的操作，但是这里有个小插曲，显示拒绝访问。
原因是忘记修改注册表，没关系，打开命令行，执行以下命令
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc" /v "Start" /t REG_DWORD /d "4" /f

2. 然后按照前面的方法，找到并定位到`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WaaSMedicSvc`，修改`FailureActions`
然后再修改“启动类型为禁用”，“恢复中的前两个选项为无操作”就没问题了
{% asset_img WaaSMedicSvc.jpg %}

*通常来讲，到这里基本已经成功禁用Windows更新了，但是为了保险起见，还要进行一点点组策略的操作。*

### 终极操作 关闭组策略及任务计划
#### 关闭组策略
    win + r -> gpedit.msc

1. 关闭配置自动更新
计算机配置 -> 管理模板 -> Windows组件 -> Windows更新，
“配置自动更新” 右键编辑，选择“已禁用”
{% asset_img gpedit.jpg 组策略 %}

2. 删除使用所有Windows更新功能的访问权限
“删除使用所有Windows更新功能的访问权限” 右键编辑，选择“已启用”
{% asset_img forbbiden_on.jpg 删除使用所有Windows更新功能的访问权限 %}

#### 关闭任务计划
    win + r -> taskschd.msc

任务计划程序库 -> Microsoft -> Windows -> WindowsUpdate
把右边的选项全都设置为禁用：
但是在禁用 sihpostreboot 的时候，提示 “你所使用的用户账户没有禁用此任务的权限”，至今未能解决。

遗憾~~~
