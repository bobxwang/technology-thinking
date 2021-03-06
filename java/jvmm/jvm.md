#### 三个角度来学习 GC 

- 哪些内存要回收

  结合 JMM，可以知道，<font color=red>堆</font> 是重点区域

  为啥分代？

  > 因为GC的整个工作过程都要"stop-the-world"，如果能有办法缩短GC一次工作的时间长度，那么说收集整个GC堆耗时太长的话不如只收集其中一部分。

  - 新生代

  - 老年代

  - 永久代

    > 1.8 后 采用元空间，就不在堆中了 

- 什么时候回收

  GC roots 

  > 通过提供一种OOPMap的数据结构，避免全内存扫描

  - 虚拟机栈(栈桢中的本地变量表)中引用的对象
  - 方法区中类静态属性引用的对象，一般指被 static 修改引用的对象
  - 方法区中的常量引用的对象
  - 本地方法栈中 JNI(native方法) 引用的对象 

  真正宣告对象死亡需要两个过程

  - 可达性分析后没有发现引用链
  - 查看对象是否有 finalize 方法，如果有且里面有自救(比如再建立引用)，那还要抢救一下

- 怎么回收

  > safepoint，GC时对一个线程来讲，要么处在 safepoint，要么不在 safepoint 

  - 三大垃圾收集算法

    - 标记/清除算法，缺点是会产生碎片且整体效率不高

    - 复制算法，缺点是内存空间消耗大，当对象存活概率高的时候开销更大

      新生代对象是"朝生夕死"的

    - 标记/整理算法，将所有存活对象移到一端再清掉边界外内存

  - 垃圾收集器 

    如果垃圾回收算法处方法论，那么收集器就是具体的实现 

    - 年轻代收集器

      Serial，ParNew，Parallel Scavenge

    - 老年代收集器

      Serial Old，Parallel Old(<font color=red>标记-整理</font>)，CMS(<font color=red>标记-清除，存在垃圾碎片</font>)

    - 特殊收集器

      G1(新型，不在年轻老年代范畴内)

    Serial 是最基本，发展最久的收集器，是单线程收集器，在收集时必须停掉其它线程，Client 模式下是个好选择，适合单核CPU

    ParNew 是 Serial 的升级版，支持多线程，是HotSpot第一个真正意义上实现并发的收集器，可通过 -XX:ParallelGCThreads 来控制垃圾收集线程的数量 

    Parallel Scavenge，采用复制算法的收集器，和 ParNew 一样支持多线程，吞吐量优先收集器

    CMS，是一种获取最短回收停顿时间为目标，重视响应，支持并发，但对堆CPU特别敏感

    G1，garbage first，尽可能多收集垃圾，避免 full gc，1.7 后才有，同 CMS 一样关注降低延迟，解决了 CMS 产生空间碎片等一系列缺陷，强化分区，弱化了分代的概念，用到了标记-整理，复制算法

#### 参数调优

-verbose:gc 输出虚拟机GC详情

XX:+PrintGCDetails 

X:loggc:log/gc.log 

XX:+TraceClassLoading

XX:+PrintClassHistogram

XX:MaxTenuringThreshold 设定通过多少次的标识后新生代存活对象移到老年代中 

XX:PretenureSizeThreshold 直接进入老年代

Xms 初始堆大小

Xmx 最大堆大小

Xss 线程栈空间大小

Xmn 新生代大小，通常为 Xmx 的1/3或1/4

XX:NewRatio 新生代老生代分配比例

XXNewSize 年轻代大小 

XX:SurvivorRatio 年轻代中Eden区跟两个Survivor区的比值 

XX:PermSize 永久代大小初始大小 

-XX:UseSerialGC 串行收集

XX:+HeapDumpOnOutMemoryError 发生OOM时导出堆的信息到文件中

XX:+HeapDumpPath 导出堆信息的文件路径

#### 生产环境的垃圾回收方法理论与实践

##### top 观察系统运行情况

##### jps 定位虚拟机进程 

列出 JVM 中运行着的进程状态信息

```shell
-l 输出main类或Jar的全限名
-q 不输出类名，Jar名和main方法传入的参数，相当于只列出进程号
-m 输出传入main方法的参数
-v 输出传入jvm的参数
```

##### jstat 定位jvm问题

``` shell
jstat -gc -pid 250 4 
```

##### jmap 导出内存转储文件

##### jstack 定位问题线程

``` shell
jstack -pid 
```

##### jhat (Heap Analysis Tool) 分析转储文件

##### 其它 visual vm/MAT/Arthas介绍 

##### GC 指标 

- GC 时间
- GC 频率 

##### 系统上线前预估系统的内存占用情况

##### 系统上线前预估系统的并发访问情况

##### 压力测试方法论

##### 根据压测结果调整参数值 

##### 系统上线后设定日志参数 

##### 定期观察日志情况

##### 根据日志解决实战问题