 	GPS车辆管理系统，是利用全球[定位技术](https://baike.baidu.com/item/定位技术)、通过无线数据传输，并配合计算机软件（MIS）实现对车辆的各项静态和动态信息进行管理。它与以往的GPS定位设备最大的不同是管理功能的多元化和系统化，突出的是计算机软件的管理功能而非定位设备功能。而与普通的车辆管理系统相比，更是因其数据动态性凸现出优势。

#####项目是干嘛的 

​		我们的项目主要利用gps开发了两个大的方向，一个是gps车辆管理，一个是手持终端管理
在车辆管理主要是针对一些车队，车辆，对他们进行不同用户，不同用途划分来提供不同的方案和不同的特殊功能。我们的系统为用户提供了车辆的实时监控，车辆的历史轨迹回放，整体车队的统计和分析，报表的产生和车辆报警和收费功能和摄像头的监控功能。

​	在手持终端管理上主要是针对一些无网网络无信号的情况下利用短报文进行通讯，定位和报警。一般我们对标救援人员或者森林深山等。

\1.   主要负责项目的设计，开发，对接，部署，文档等

\7.   负责车辆监控模块开发：获取终端上报的信息用于前台展示车辆的位置信息

\8.   负责电子围栏和规则管理模块：自定义区域标记，并在规则处定义超速，禁入，禁出等规则

\9.   负责报警监控模块：监控车辆位置状态，如发现违规，则触发报警，

\10.  负责日志模块开发：利用spring的aop技术实现动态切入日志管理

\11.  负责项目直播模块的开发： 利用nginx-rtmp插件，实现视频的直播和录播功能

#### 项目的架构

整个项目是基于boot和cloud进行开发的

- 系统分为三个服务，分别是车辆监控项目，系统项目，和短报文项目
- 我们整体架构使用了三台阿里云服务器，
- 其中mq，reids，mysql放在一台服务器
- service和controller放在同一台服务器上
- nginx放在一台服务器上，用来做反向代理和动静分离、
- 我们使用eureka做的服务注册中心
- 配置中心使用的cloud的config配置中心
- 使用mq来实时刷新客户端的配置信息，还有我们的项目用mq来接收上报的终端实时信息
- 我们使用的openfeign做服务间的通信，当然还可以用它做降级和熔断
- 使用redis搭建缓存数据库，用来存放缓存数据和终端上传的位置信息等





##### 你负责了那些模块  

​	其中我主要负责gps车辆管理相关的模块，具体有：

- 车辆监控
  - 通过终端上报的定位信息，在地图上标记出车辆所在的位置和所需
- 信息报表
  - 
- 轨迹回放
  - 可以查询时间段内车辆的运行轨迹，并可以导出表格
- 报警监控
  - 我们利用socket来实时推送报警信息，在mq上传服务器的时候进行报警判断，并进行实时推送，在平台发送警告
- 电子围栏
  - 我们在地图上可自定义区域围栏，来辅助对车辆的规则管理
- 规则管理
  - 主要是对车辆的超速，禁入，禁出，进行判断和报警，通过对
- 日志系统
- 视频监控
  - 我们利用nginx-rtmp插件对车辆摄像头上传的视频信息进行直播和录制，通过配置nginx的配置文件，主要是开启live模式和record模式，进行直播和录播



##### 使用了那些技术为什么使用这些技术

​	我们使用的是sprigboot+cloud框架 搭建我们的

​	然后我们的数据库使用的是oracle

​	我们的缓存使用的是redis和ssdb

​	中间件使用的是activemq **分析下为什么选择他**

 ![img](https://img-blog.csdnimg.cn/20190319201723473.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvemh4eQ==,size_16,color_FFFFFF,t_70) 

分布式框架用的是dubbo和zookepper： **好处详情**





#### 你负责的模块遇到了那些问题，如何解决的 [亮点/难点]



arraylist循环迭代删除时候发生了报错

使用copyonwritarraylist代替

因为底层是复制，所以jvm频繁发生gc

然后对jvm进行优化



什么时候用到的

他解决了什么问题







##### 你负责的模块遇到了那些问题，如何解决的 [亮点/难点]

##### 项目上线后会周期性出现的运行缓慢，出现卡顿现象,对其进行jvm优化

##### 项目出现的问题，如何定位

​	项目在上线后出现了周期性的运行缓慢，卡顿现象，随后查询了日志发现了大量fullgc的调用。

**配置gc日志输出的命令**

```shell
-XX:+PrintGCDetails 输出GC的详细日志
```

**GC日志查看**

```shell
[Full GC (System.gc())  ：[Full Gc(类型，原因)

[PSYoungGen: 1403K->0K(917504K)] ：[新生代：原使用内存->现使用内存(整个堆中的新生代的内存大小)]

[ParOldGen: 8K->1261K(1048576K)] ：[老年代：原使用内存->现使用内存(整个堆中的老年代的内存大小)]

1411K->1261K(1966080K), ：[gc发生前堆内存大小->gc发生后堆内存大小(整个堆的内存大小)]

[Metaspace: 3508K->3508K(1056768K)], ：[元空间：gc前元空间使用内存->gc后使用内存(元空间总内存)]

0.0083870 secs] ：Full Gc总消耗时间]

[Times: user=0.01 sys=0.00, real=0.01 secs]：[]
```

查看日志发现问题所在，多次fullgc 时间间隔较短，而且gc时间较长。



服务器的物理内存是16gb 

通过命令查看内存的初始大小和最大值

```shell
在Windows里：
java -XX:+PrintFlagsFinal -version | findstr /i "HeapSize PermSize ThreadStackSize"
在Linux里：
java -XX:+PrintFlagsFinal -version | grep -iE 'HeapSize|PermSize|ThreadStackSize'
————————————————
java -XshowSettings:all
```

堆默认的最大值为4gb，初始值为250mb也就是物理内存的64分之一





第一步优化：将xms和xmx设置为一样的，和新生代的大小



当时出现这种情况以为是网络延迟或者其他症状，后来经过xxx证明不是这种状况，然后又觉得是不是jvm的原因，于是用命令 jstat 去查询虚拟机的各项指标，发现fullgc频繁执行，消耗的时间很长，因此发现是jvm频繁gc导致的性能下降， 项目周期性的把老年代填满，所以jvm周期性的去进行fullgc回收垃圾，（**日志如何配置开启的**）查看日志的时候,发现gc前后堆的大小有变化，认为gc后堆的内存大小变化导致了系统的性能不稳定，所以去设置了-xms 和-xmx 最小值和最大值保持了一致，这里我们根据系统**xxx**去设定的大小（**合理的大小**），这样系统可以避免内存不够发生gc以及在 GC 后调整堆大小带来的压力 。设置完成之后发现会出现大量fullgc的情况，使用jmap -histo：live pid 查询对象占用的大小，发现数组占用的最大，要去查询是哪个对象的引用最多，



我们项目出现过周期性卡顿的原因，我们首先排除了网络上的问题，然后对服务器进行排查，再一次卡顿的情况下去查看了服务器的cpu使用率，发现挺高的，感觉是内存出现了问题，然后使用jvm的自带工具对内存进行分析，首先我们使用jstat -gcutil 命令查询jvm运行时的状态信息， 发现在短时间内发生了大量的fullgc，因此得出结论，是频繁的fullgc导致系统卡顿，这里分析 什么原因可以导致fullgc频发触发呢， 1.老年代太小 2.内存泄漏首先我们分析了下代码，觉得内存泄露（指的是无法被回收的内存）的可能性不大，然后去检查了下jvm的参数设置，发现初始化的堆大小和堆最大值不相等，随后将其设置为相同，在看gc发现频率下降

通过这次问题排查，我又去深入了解了下jvm ，个人认为调优需要解决的问题有两点

1.gc执行时间长，原因在于老年代的垃圾比较多，回收比较慢， 

第一适当增加新生代大小，较少对象进入老年代

改用cms收集器，cms并发处理垃圾，可减少线程阻塞时间

2.gc执行间隔短

1.初始值和最大值设置为相同，每次达到最大值会发生gc然后内存动态改变，会出现频繁gc和浪费性能

最大值不宜太小或太大，根据实际测试设置





**jps查询java进程id**

```shell
[root@iZ2ze5k93swqrj5t9r59fnZ /]# jps

7874 Jps
8939 Bootstrap
```



 **jstat  -gcutil 垃圾回收统计概述 主要用来查看JVM运行时的状态信息，包括内存状态、垃圾回收等。** 

```shell
jstat -gcutil 8939 1000 10 //1666为pid，每隔1000毫秒打印一次，一共打印10次
```

```shell
[root@iZ2ze5k93swqrj5t9r59fnZ /]# jstat -gcutil 8939 1000 3
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
 36.29   0.00  98.52  84.17  97.32  94.55   6654   34.704     6    0.366   35.070
 36.29   0.00  99.41  84.17  97.32  94.55   6654   34.704     6    0.366   35.070
 36.29   0.00  99.41  84.17  97.32  94.55   6654   34.704     6    0.366   35.070
 
so：survivor0使用百分比
s1：survivor1使用百分比
E：Eden使用百分比
O：老年代使用百分比
M：元空间使用百分比
CCS：压缩使用百分比
YGC：新生代gc次数
YGCT：新生代gc消耗时间
FGC：老年代gc次数
FGCT：老年代gc消耗时间
GCT：垃圾回收消耗总时间
```



**jstack 是用来查看JVM线程快照的命令** 



 **jmap -heap是用来生成堆dump文件和查看堆相关的各类信息的命令** 

```shell
jmap -heap 11666 // 根据pid查询堆栈的信息
```

```shell
Attaching to process ID 11666, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.25-b02

using thread-local object allocation.
Parallel GC with 4 thread(s)

Heap Configuration: //堆内存初始化配置
   MinHeapFreeRatio         = 0 //对应jvm启动参数-XX:MinHeapFreeRatio设置JVM堆最小空闲比率(default 40)
   
   MaxHeapFreeRatio         = 100 //对应jvm启动参数 -XX:MaxHeapFreeRatio设置JVM堆最大空闲比率(default 70)
   
   MaxHeapSize              = 1073741824 (1024.0MB) //对应jvm启动参数-XX:MaxHeapSize=设置JVM堆的最大大小
   
   NewSize                  = 22020096 (21.0MB) //对应jvm启动参数-XX:NewSize=设置JVM堆的新生代的默认大小
   
   MaxNewSize               = 357564416 (341.0MB) //对应jvm启动参数-XX:MaxNewSize=设置JVM堆的新生代的最大大小
   
   OldSize                  = 45088768 (43.0MB) //对应jvm启动参数-XX:OldSize=<value>:设置JVM堆的老年代的大小
   
   NewRatio                 = 2 //对应jvm启动参数-XX:NewRatio=:新生代和老生代的大小比率
   SurvivorRatio            = 8 //对应jvm启动参数-XX:SurvivorRatio=设置新生代中Eden区与Survivor区的大小比值
   MetaspaceSize            = 21807104 (20.796875MB) // 元数据区大小
   CompressedClassSpaceSize = 1073741824 (1024.0MB) //类压缩空间大小
   MaxMetaspaceSize         = 17592186044415 MB //元数据区最大大小
   G1HeapRegionSize         = 0 (0.0MB) //G1垃圾收集器每个Region大小

Heap Usage: //堆内存使用情况
PS Young Generation 
Eden Space: //Eden区内存分布
   capacity = 17825792 (17.0MB) //Eden区总容量
   used     = 12704088 (12.115562438964844MB) //Eden区已使用
   free     = 5121704 (4.884437561035156MB) //Eden区剩余容量
   71.26801434685203% used //Eden区使用比率
From Space: //其中一个Survivor区的内存分布
   capacity = 2097152 (2.0MB)
   used     = 1703936 (1.625MB)
   free     = 393216 (0.375MB)
   81.25% used
To Space: //另一个Survivor区的内存分布
   capacity = 2097152 (2.0MB)
   used     = 0 (0.0MB)
   free     = 2097152 (2.0MB)
   0.0% used
PS Old Generation
   capacity = 52428800 (50.0MB) //老年代容量
   used     = 28325712 (27.013504028320312MB) //老年代已使用
   free     = 24103088 (22.986495971679688MB) //老年代空闲
   54.027008056640625% used //老年代使用比率
interned Strings occupying 2075304 bytes.
```





 **jmap -histo pid 展示class的内存情况   JVM会先触发gc，然后再统计信息。** 

```shell
jmap -histo:live 8939
```

```shell
 num     #instances         #bytes  class name
----------------------------------------------
   1:         87496       11377592  [C
   2:          2166        3988224  [B
   3:         10269        2542792  [I
   4:         86274        2070576  java.lang.String
   5:         62490        1999680  java.util.HashMap$Node
   6:          9517        1069320  java.lang.Class
   7:          4485         164008  [Ljava.lang.String;
----------------------------------------------
   instances：对象的实例个数
   bytes：总占用的字节数
   class name ：对应Class文件里的文件标识
   B 代表 byte
   C 代表 char
   D 代表 double
   F 代表 float
   I 代表 int
   J 代表 long
   Z 代表 boolean
   前边有 [ 代表数组， [I 就相当于 int[]
   对象用 [L+ 类名表示
```



生成我们需要分析的dump文件

```shell
jmap -dump:live,format=b,file=dumpfile pid
```

将我们需要分析的文件下载到本地， 用jdk自带的 VisualVM 和Jconsole 来进行分析文件。







查看日志只是能看到最后导致oom的直接原因而分析dump文件可以看到导致运行慢的根本原因

所以要提前设置出现异常自动生成dump文件 

XX：+HeapDumpOnOutOfMemoryError可以让虚拟机在出现内存溢出异常时Dump出当前的内存堆转储快照以便事后进行分析 

​	存放的数据太多，进行定时删除历史数据



##### 你的这些问题是如何解决的

##### 项目亮点是什么

一个组长，两个java开发，两个前端，一个c语言，一个测试，一个ui

##### 你们开发项目用了多少时间  

整个项目第一版上线用了六个月左右，后续的话项目不断更新上新。

##### 你们的用户量是多少  

我们的用户是针对企业的
  高新区
   明港镇  平桥区
  新干县
  羊山新区
  浉河区
  南湾湖
  上天梯
10 * 50 = 500辆车。   

##### 你们有没有并发

##### 你项目里有什么亮点？

我们的项目对数据要求比较大，忙的时候平均每小时要处理几十万条数据