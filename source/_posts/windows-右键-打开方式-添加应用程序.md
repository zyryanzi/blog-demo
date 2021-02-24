---
title: windows-右键-打开方式-添加应用程序
date: 2021-02-23 09:27:21
tags: 
    - 系统
    - windows技巧
categories:
    - 计算机科学
    - 系统
---

*一个小技巧*
<!--more-->
### 打开 regedit
    win + r -> 输入 “regedit”
{% asset_img regedit.jpg open regedit %}

### 进入 HKEY_CLASSES_ROOT -> * -> shell
{% asset_img shell.jpg 进入 shell 文件夹 %}

### shell 上右键 -> 新建 -> 项
{% asset_img new-option.jpg 新建项 %}
并输入名称（如 “EditPlus”）

### 再新建项，并命名为 "command"
{% asset_img new-command.jpg 新建项 %}
并双击右侧 “默认” 图标

### 编辑 默认项
{% asset_img edit-moren.jpg 编辑字符串 %}
将应用程序的路径复制过来，注意要在路径后面加上应用程序名称，例如：\EditPlus.exe。最后，在路径和名称后面加上"空格"和"%1"。
例如：E:\Program_Files\EditPlus\EditPlus.exe 1%