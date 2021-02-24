---
title: hexo-next 使用tags、categories等菜单
date: 2021-02-23 17:56:22
tags:
    - hexo
    - next
    - menu
categories:
    - 计算机科学
    - hexo
---

*next 主题中，tags、categories 等菜单默认关闭的，在配置文件中将其放开即可显示。然鹅~~~*
*页面上点开就出现了 “Cannot GET /tags/”，于是，拜了几个山头，讨回了一点经验*
*感谢大佬：https://www.lwhweb.com/posts/765/*
<!--more-->
### 新建页面，以tags为例：
    hexo new page "tags"

执行以上命令，即可生成 /source/tags/index.md 文件，这样可以确保在打包时在 /tags/下面生成index.html
这也是问题的关键所在

### 修改新生成的 /source/tags/index.md，加入 "type: tags"
    ---
    title: tags（自己定义，显示在页面）
    date: 2021-02-23 17:23:37
    type: tags
    ---

### 修改主题的配置文件，例如：_config.next.yml
{% asset_img release_conf.jpg 放开菜单配置 %}

放开对应菜单的配置，如果没有，添加即可

*至此结束*