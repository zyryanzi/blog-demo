---
title: Java-多线程-如何设置线程数
date: 2021-06-01 15:16:25
tags:
    - Java
    - Thread
categories:
    - 计算机科学
    - Java
---

*一直想学好多线程，一直想了解一些多线程的相关知识，看到一篇浅显易懂的文章，搬来留作记录*
<!--more-->

### 旧有线程数设置理论
1、 CPU 密集型的程序 - 核心数 + 1
2、 I/O 密集型的程序 - 核心数 * 2

### 大神测试小结
1、 一个极端的线程（不停执行“计算”型操作时），就可以把单个核心的利用率跑满，多核心CPU最多只能同时执行等于核心数的“极端”线程数。
2、 如果每个线程都这么“极端”，且同时执行的线程数超过核心数，会导致不必要的切换，造成负载过高，只会让执行更慢。
3、 I/O 等暂停类操作时，CPU处于空闲状态，操作系统调度CPU执行其他线程，可以提高CPU利用率，同时执行更多的线程。
4、 I/O 事件的频率频率越高，或者等待/暂停时间越长，CPU的空闲时间也就更长，利用率越低，操作系统可以调度CPU执行更多的线程。

### 重新得到线程数公式

N<sub>cpu</sub> : CPU核心数
U<sub>cpu</sub> : 目标CPU利用率，0 <= U<sub>cpu</sub> <=1
W/C : 等待时间和计算时间的比例

如此希望CPU达到目标利用率，则公式为：

N<sub>threads</sub> = N<sub>cpu</sub> * U<sub>cpu</sub> * (1 + w/c)

虽然公式很好，但在真实的程序中，一般很难获得准确的等待时间和计算时间，因为程序很复杂，不只是“计算” 。一段代码中会有很多的内存读写，计算，I/O 等复合操作，精确的获取这两个指标很难，所以光靠公式计算线程数过于理想化。

### 生产中如何确定线程数
那么在实际的程序中，或者说一些Java的业务系统中，线程数（线程池大小）规划多少合适呢？
没有固定答案，先设定预期，比如我期望的CPU利用率在多少，负载在多少，GC频率多少之类的指标后，再通过测试不断的调整到一个合理的线程数。

比如一个普通的，SpringBoot 为基础的业务系统，默认Tomcat容器+HikariCP连接池+G1回收器，如果此时项目中也需要一个业务场景的多线程（或者线程池）来异步/并行执行业务流程。
此时我按照上面的公式来规划线程数的话，误差一定会很大。因为此时这台主机上，已经有很多运行中的线程了，Tomcat有自己的线程池，HikariCP也有自己的后台线程，JVM也有一些编译的线程，连G1都有自己的后台线程。这些线程也是运行在当前进程、当前主机上的，也会占用CPU的资源。

所以受环境干扰下，单靠公式很难准确的规划线程数，一定要通过测试来验证。

### 流程
1、 分析当前主机上，有没有其他进程干扰
2、 分析当前JVM进程上，有没有其他运行中或可能运行的线程
3、 设定目标
4、 目标CPU利用率 - 我最高能容忍我的CPU飙到多少？
    目标GC频率/暂停时间 - 多线程执行后，GC频率会增高，最大能容忍到什么频率，每次暂停时间多少？
    执行效率 - 比如批处理时，我单位时间内要开多少线程才能及时处理完毕
5、 梳理链路关键点，是否有卡脖子的点，因为如果线程数过多，链路上某些节点资源有限可能会导致大量的线程在等待资源（比如三方接口限流，连接池数量有限，中间件压力过大无法支撑等）
6、 不断的增加/减少线程数来测试，按最高的要求去测试，最终获得一个“满足要求”的线程数

而且而且而且！不同场景下的线程数理念也有所不同：

1、 Tomcat中的maxThreads，在Blocking I/O和No-Blocking I/O下就不一样
2、 Dubbo 默认还是单连接呢，也有I/O线程（池）和业务线程（池）的区分，I/O线程一般不是瓶颈，所以不必太多，但业务线程很容易称为瓶颈
3、 Redis 6.0以后也是多线程了，不过它只是I/O 多线程，“业务”处理还是单线程
所以，不要纠结设置多少线程了。没有标准答案，一定要结合场景，带着目标，通过测试去找到一个最合适的线程数。

可能还有同学可能会有疑问：“我们系统也没啥压力，不需要那么合适的线程数，只是一个简单的异步场景，不影响系统其他功能就可以”
很正常，很多的内部业务系统，并不需要啥性能，稳定好用符合需求就可以了，那么我的推荐的线程数是：**CPU核心数**
Java 获取CPU核心数

    Runtime.getRuntime().availableProcessors()
    //获取逻辑核心数，如6核心12线程，那么返回的是12

Linux 获取CPU核心数

    # 总核数 = 物理CPU个数 X 每颗物理CPU的核数
    # 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

    # 查看物理CPU个数
    cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

    # 查看每个物理CPU中core的个数(即核数)
    cat /proc/cpuinfo| grep "cpu cores"| uniq

    # 查看逻辑CPU的个数
    cat /proc/cpuinfo| grep "processor"| wc -l

**感谢搜云的文章**
