---
title: Github Login 对话框问题的解决
date: 2021-03-02 17:29:05
tags: 
    - github
    - 小技巧
categories:
    - 计算机科学
    - git
---

*曾经有一个问题，困扰了我很久。每每询问搜索引擎，都不得法。这次终于意外的发现了真谛！*
*这是一个关于 git pull 对话框的问题*
<!--more-->

### 问题一：Github Login 的问题
{% asset_img login.jpg github login 对话框 %}

上面这个对话框是我心里永远的痛，不知道什么时候突然出现在我 git pull 的路上，驱之不走，挥之不去。
而且每次输入正确的用户名密码，它都不认；即使将 Github Desktop 卸载了也依然无济于事。
终于忍不住搜了一下，原来是一个参数惹的祸，没错，就是它：

    credential.helper

使用下面的命令查看您的git config:

    git config --list

就会发现：

    credential.helper=manager

很简单，使用如下命令即可解决：

    git config --system --unset credential.helper

*然而，不经意间，发现了另外一个秘密*
### 问题二：每次 git pull 输入用户名密码
{% asset_img store.jpg github login store %}
这里面说这个值：可以存储身份验证凭据。
那是不是我这些使用 http 协议克隆下来的代码，都可以只进行首次身份验证了？
没想到竟然意外的解决了另一个顽疾。
执行以下命令：

    git config --system credential.helper 'store'

这时再查看 git config，已经变了，如下：

    credential.helper=store

### 总结
*想要好好解决问题，官方文档才是正道。*
*从前解决这个问题的时候，查到的帖子，全都是让我用使用ssh凭证，我想这个大家都知道。*
*还有一点，不知道为啥，我的命令全都要加上 `--system`，不然不生效。容后再论*
