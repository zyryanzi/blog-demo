---
title: git 子模块 submodule 添加-使用-删除
date: 2021-02-06 10:30:52
tags: ready-go
category: 计算机科学
    git
---

**刚开始搞blog，使用 hexo 的 next 模板的时候，直接从github上拉了代码，这样在我的仓库下面就有了另外一个仓库。为了能尽量避坑，搜索了一些前人的经验，记录于此。**
<!-- more -->
## 子模块添加
执行如下命令：

    git submodule add https://github.com/next-theme/hexo-theme-next.git <yourpath/modulename>

例：

    git submodule add https://github.com/next-theme/hexo-theme-next.git themes/hexo-theme-next/

就会生成以下文件：

    /.gitmodules
    /yourpath
    /.git/modules/<modulename>(不知道为啥第二次我这里没有生成)

影响以下文件：

    /.git/config文件会增加 submodule 的条目

查看修改内容即可看到上述修改:
    
    git diff --cached

提交以完成添加：

    git commit
    
## 子模块使用
如果是自己新建的子模块这时路径下还没有代码，需要初始化：

    git submodule init
    git submodule update

如果是用别人的仓库，这时应该就已经有了代码。

## 子模块的更新
子模块的维护者提交更新后，我们要自己去手动获取更新，在父仓库目录：
### PULL
更新别人加的 submodule：

    git pull
    git submodule init
    git submodule update

更新某个 submodule：

    git submodule update -- <path>

一次更新全部：

    git submodule foreach git pull origin master

### PUSH
~ 先在 submodule 的目录下 push (单独更新 submodule)
~ 然后在父仓库的目录下 push (更新父仓库的 submodule)
    
## 子模块的删除
这个过程有点麻烦，要分别删除添加子模块时生成的那些

~ 删除子模块目录及源码；

~ 删除 /.gitmodules 文件中目标子模块相关条目；

~ 删除 /.git/config 中目标子模块相关条目；

~ 删除 /.git/modules/ 目标子模块目录，每个子模块对应一个目录。

#### *最后，请记得清除缓存*
    
    git rm --cached 子模块名称

完成删除后，提交到仓库即可

    git commit

Tips: *这样做应该有一些好处，我还没学到，随后更新吧。*