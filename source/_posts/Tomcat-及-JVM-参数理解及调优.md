---
title: Tomcat 及 JVM 参数理解及调优
date: 2021-03-12 09:45:49
tags:
    - Java
    - JVM
    - Tomcat

categories:
    - 计算机科学
    - Java
---

*调优 一直是感觉比较模糊，迷幻的东西，欲求之而不得，留文于此，以备日后研读*
<!--more-->
### 为什么要进行优化
Tomcat 的缺省配置是不能稳定长期运行的，也就是不适合生产环境，它会死机，让你不断重新启动。对于操作系统优化来说，是尽可能的增大可使用的内存容量、提高CPU 的频率，保证文件系统的读写速率等。经过压力测试验证，在并发连接很多的情况下，CPU 的处理能力越强，系统运行速度越快。
Tomcat 的优化比较复杂，主要有三方面：系统优化，Tomcat 本身的优化，JVM调优

### Tomcat 本身的优化
系统优化看不大懂，直接调整配置文件，Connector  默认就启用的 Apr 协议，但需要系统安装 Apr 库，否则就会使用 bio 方式。
打开 ${TOMCAT_HOME}/conf/server.xml，搜索`<Executor name="tomcatThreadPool"`，修改为

    <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="500" minSpareThreads="20" maxSpareThreads="50" maxIdleTime="60000"/>

然后，修改<Connector …>节点，增加 executor 属性，搜索【port="8080"】，修改为

    <Connector executor="tomcatThreadPool"
        port="8080" protocol="HTTP/1.1"
        URIEncoding="UTF-8"
        connectionTimeout="30000"
        enableLookups="false"
        disableUploadTimeout="false"
        connectionUploadTimeout="150000"
        acceptCount="300"
        keepAliveTimeout="120000"
        maxKeepAliveRequests="100"
        compression="on"
        compressionMinSize="2048"
        compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain,image/gif,image/jpg,image/png" 
        redirectPort="8443" />

说明：
maxThreads :Tomcat 使用线程来处理接收的每个请求，这个值表示 Tomcat 可创建的最大的线程数，默认值是 200
minSpareThreads：最小空闲线程数，Tomcat 启动时的初始化的线程数，表示即使没有人使用也开这么多空线程等待，默认值是 10。
maxSpareThreads：最大备用线程数，一旦创建的线程超过这个值，Tomcat 就会关闭不再需要的 socket 线程。
上边配置的参数，最大线程 500（一般服务器足以），要根据自己的实际情况合理设置，设置越大会耗费内存和 CPU，因为 CPU 疲于线程上下文切换，没有精力提供请求服务了，最小空闲线程数 20，线程最大空闲时间 60 秒，当然允许的最大线程连接数还受制于操作系统的内核参数设置，设置多大要根据自己的需求与环境。
***当然线程可以配置在“tomcatThreadPool”中，也可以直接配置在“Connector”中，但不可以重复配置。***
URIEncoding：指定 Tomcat 容器的 URL 编码格式，语言编码格式这块倒不如其它 WEB 服务器软件配置方便，需要分别指定。
connnectionTimeout： 网络连接超时，单位：毫秒，设置为 0 表示永不超时，这样设置有隐患的。通常可设置为 30000 毫秒，可根据检测实际情况，适当修改。
enableLookups： 是否反查域名，以返回远程主机的主机名，取值为：true 或 false，如果设置为false，则直接返回IP地址，为了提高处理能力，应设置为 false。
disableUploadTimeout：上传时是否使用超时机制。
connectionUploadTimeout：上传超时时间，毕竟文件上传可能需要消耗更多的时间，这个根据你自己的业务需要自己调，以使Servlet有较长的时间来完成它的执行，需要与上一个参数一起配合使用才会生效。
acceptCount：指定当所有可以使用的处理请求的线程数都被使用时，可传入连接请求的最大队列长度，超过这个数的请求将不予处理，默认为100个。
keepAliveTimeout：长连接最大保持时间（毫秒），表示在下次请求过来之前，Tomcat 保持该连接多久，默认是使用 connectionTimeout 时间，-1 为不限制超时。
maxKeepAliveRequests：表示在服务器关闭之前，该连接最大支持的请求数。超过该请求数的连接也将被关闭，1表示禁用，-1表示不限制个数，默认100个，一般设置在100~200之间。
compression：是否对响应的数据进行 GZIP 压缩，off：表示禁止压缩；on：表示允许压缩（文本将被压缩）、force：表示所有情况下都进行压缩，默认值为off，压缩数据后可以有效的减少页面的大小，一般可以减小1/3左右，节省带宽。
***此处应注意和其他软件(如 nginx 的 gzip)的压缩重复，会出现bug***
compressionMinSize：表示压缩响应的最小值，只有当响应报文大小大于这个值的时候才会对报文进行压缩，如果开启了压缩功能，默认值就是2048。
compressableMimeType：压缩类型，指定对哪些类型的文件进行数据压缩。
noCompressionUserAgents="gozilla, traviata"： 对于以下的浏览器，不启用压缩。

如果已经对代码进行了动静分离，静态页面和图片等数据就不需要 Tomcat 处理了，那么也就不需要配置在 Tomcat 中配置压缩了。
以上是一些常用的配置参数属性，当然还有好多其它的参数设置，还可以继续深入的优化，HTTP Connector 与 AJP Connector 的参数属性值，可以参考官方文档的详细说明：
https://tomcat.apache.org/tomcat-7.0-doc/config/http.html
https://tomcat.apache.org/tomcat-7.0-doc/config/ajp.html

### JVM 优化

Tomcat 启动命令行中的优化参数，就是 JVM 的优化 。Tomcat 首先跑在 JVM 之上的，因为它的启动其实也只是一个 java 命令行，首先我们需要对这个 JAVA 的启动命令行进行调优。
不管是 YGC 还是 Full GC，GC 过程中都会对导致程序运行中中断，正确的选择不同的 GC 策略，调整 JVM、GC 的参数，可以极大的减少由于 GC 工作，而导致的程序运行中断方面的问题，进而适当的提高 Java 程序的工作效率。

#### JVM 参数配置方法

Tomcat 的启动参数位于安装目录 ${JAVA_HOME}/bin目录下，Linux 操作系统就是 catalina.sh 文件。
JAVA_OPTS，就是用来设置 JVM 相关运行参数的变量，还可以在 CATALINA_OPTS 变量中设置。关于这 2 个变量，还是多少有些区别的：
JAVA_OPTS：用于当 Java 运行时选项 `start`、`stop` 或 `run` 命令执行。
CATALINA_OPTS：用于当 Java 运行时选项 `start` 或 `run` 命令执行。

为什么有两个不同的变量？它们之间都有什么区别呢？

首先，在启动 Tomcat 时，任何指定变量的传递方式都是相同的，可以传递到执行 `start` 或 `run` 命令中，但只有设定在 JAVA_OPTS 变量里的参数被传递到`stop`命令中。对于 Tomcat 运行过程，可能没什么区别，影响的是结束程序，而不是启动程序。

第二个区别是更微妙，其他应用程序也可以使用 JAVA_OPTS 变量，但只有在 Tomcat 中使用 CATALINA_OPTS 变量。如果你设置环境变量为只使用 Tomcat，最好你会建议使用 CATALINA_OPTS 变量，而如果你设置环境变量使用其它的 Java 应用程序，例如 JBoss，你应该把你的设置放在JAVA_OPTS 变量中。

#### JVM 参数属性

32 位系统下 JVM 对内存的限制：不能突破 2GB ，那么这时你的 Tomcat 要优化，就要讲究点技巧了，
而在 64 位操作系统上无论是系统内存还是 JVM 都没有受到 2GB 这样的限制。

针对于 JMX 远程监控也是在这里设置，以下为 64 位系统环境下的配置，内存加入的参数如下：（以下内容如果报错，建议放一行，把双引号去掉可解决）

    set CATALINA_OPTS="
    -server 
    -Xms6000M 
    -Xmx6000M 
    -Xss512k 
    -XX:NewSize=2250M 
    -XX:MaxNewSize=2250M 
    -XX:PermSize=128M
    -XX:MaxPermSize=256M  
    -XX:+AggressiveOpts 
    -XX:+UseBiasedLocking 
    -XX:+DisableExplicitGC 
    -XX:+UseParNewGC 
    -XX:+UseConcMarkSweepGC 
    -XX:MaxTenuringThreshold=31 
    -XX:+CMSParallelRemarkEnabled 
    -XX:+UseCMSCompactAtFullCollection 
    -XX:LargePageSizeInBytes=128m 
    -XX:+UseFastAccessorMethods 
    -XX:+UseCMSInitiatingOccupancyOnly
    -Duser.timezone=Asia/Shanghai 
    -Djava.awt.headless=true"

Linux：
通常我们不在 catalina.sh 文件中直接设置参数，可以在同级目录新建一个 `setenv.sh`，然后写入如下配置

    #!/bin/shexport

    # 设置JAVA_HOME
    export JAVA_HOME=/usr/java/jdk1.8.0_281-amd64
    export PATH=$PATH:$JAVA_HOME/bin
    export CLASSPATH=$JAVA_HOME/lib

    # 设置CATALINA_HOME
    CATALINA_HOME=/usr/local/apache-tomcat-8.5.63

    # -Xms：虚拟机初始化时的最小堆内存。
    # -Xmx：虚拟机可使用的最大堆内存. -Xms与-Xmx设成一样的值，避免JVM因为频繁的GC导致性能大起大落
    # 设定每个线程的堆栈大小。一般不宜设置超过1M，要不然容易出现out ofmemory
    # AggressiveOpts: 启用这个参数，则每当JDK版本升级时，你的JVM都会使用最新加入的优化技术（如果有的话）
    # UseBiasedLocking: 启用一个优化了的线程锁。对于应用服务器来说每个http请求就是一个线程，由于请求需要的时长不一，在并发较大的时候会有请求排队、甚至还会出现线程阻塞的现象，这个配置可以改善这个问题。
    # PermSize: 设置非堆内存初始值，默认是物理内存的1/64；
    # MaxPermSize: 设置最大非堆内存的大小，默认是物理内存的1/4。
    # DisableExplicitGC: 在程序代码中不允许有显示的调用”System.gc()”。
    # UseParNewGC: 对年轻代采用多线程并行回收，这样收得快。
    # MaxTenuringThreshold: 设置垃圾最大年龄。如果设置为0的话，则年轻代对象不经过Survivor区，直接进入年老代,根据本地的jprofiler监控后得到的一个理想的值，不能一概而论原搬照抄。
    # UseConcMarkSweepGC: 即CMS gc，这一特性只有jdk1.5即后续版本才具有的功能，它使用的是gc估算触发和heap占用触发。
    # CMSParallelRemarkEnabled: 在使用 UseParNewGC 的情况下，尽量减少 mark 的时间。
    # UseCMSCompactAtFullCollection: 在使用 concurrent gc 的情况下，防止 memoryfragmention，对 live object 进行整理，使 memory 碎片减少。
    # LargePageSizeInBytes: 指定 Java heap 的分页页面大小，内存页的大小不可设置过大， 会影响 Perm 的大小。
    # UseFastAccessorMethods: 使用 get，set 方法转成本地代码，原始类型的快速优化。
    # UseCMSInitiatingOccupancyOnly: 只有在 oldgeneration 在使用了初始化的比例后 concurrent collector 启动收集。
    # UseBiasedLocking: -Djava.awt.headless=true这个参数一般我们都是放在最后使用的, 解决window下可以显示的图片在linux不能显示

    export JAVA_OPTS="-server     
        -Xms512M
        -Xmx512M
        -Xss512k
        -XX:+AggressiveOpts
        -XX:+UseBiasedLocking
        -XX:PermSize=16M
        -XX:MaxPermSize=256M
        -XX:+DisableExplicitGC     
        -XX:+UseParNewGC
        -XX:MaxTenuringThreshold=8 
        -XX:+UseConcMarkSweepGC
        -XX:+CMSParallelRemarkEnabled     
        -XX:+UseCMSCompactAtFullCollection 
        -XX:LargePageSizeInBytes=64m 
        -XX:+UseFastAccessorMethods 
        -XX:+UseCMSInitiatingOccupancyOnly 
        -Djava.awt.headless=true "

本文参考：
https://blog.csdn.net/adrian_dai/article/details/80538059
https://blog.csdn.net/y_blueblack/article/details/81066141