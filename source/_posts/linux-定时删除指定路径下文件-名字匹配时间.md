---
title: Linux-定时删除指定路径下文件-名字匹配时间
date: 2021-03-29 16:15:11
tags:
    - Linxu
    - Shell
categories:
    - 计算机科学
    - 系统
---

*今日份折腾，需要定时删除 NAS 中的监控视频文件，不然会太多了*
<!--more-->

### 脚本
脚本很简单：

    #!/bin/sh
    find /path -name `date -d"-3 day" +%Y%m%d`* -type d -exec rm -rf {} \;

学习一下其中知识点。

### find 命令
因为路径下是分文件夹存储，而文件夹命名规则就是日期，上述命令大概可以如此解读
`find`：Linux下一条很有用的查找命令；
`/path`：想要查找的路径；
`-name`：查询参数，按命名查找；
`date`：shell 中获得系统时间的方法，默认格式：Fri Nov 18 10:38:07 CST 2021；
`date +%Y%m%d`：对日期输出进行了格式化，变成 20211118；
`date -d"-3 day"`：由于我是想要清除三天前的，所以要用到这个；
`-type`：文件类型，`d`是文件夹，`f`是文件……；
`-exec **** {}`：对查找出来的内容进行 **** 的操作；

### crontab 定时执行
创建定时任务：

    crontab -e
    10 4 1 * * /bin/sh /root/soft_shell/delbak.sh

-e:    编辑crontab定时任务，会打开vim编辑你的工作
-l:    查询crontab任务
-r:    删除当前用户所有的crontab任务

### find 命令扩展
- -amin n : 在过去 n 分钟内被读取过
- -anewer file : 比文件 file 更晚被读取过的文件
- -atime n : 在过去n天内被读取过的文件
- -cmin n : 在过去 n 分钟内被修改过
- -cnewer file :比文件 file 更新的文件
- -ctime n : 在过去n天内被修改过的文件
- -empty : 空的文件-gid n or -group name : gid 是 n 或是 group 名称是 name
- -ipath p, -path p : 路径名称符合 p 的文件，ipath 会忽略大小写
- -name name, -iname name : 文件名称符合 name 的文件。iname 会忽略大小写
- -size n : 文件大小 是 n 单位，b 代表 512 位元组的区块，c 表示字元数，k 表示 kilo bytes，w 是二个位元组。
- -type c : 文件类型是 c 的文件。
