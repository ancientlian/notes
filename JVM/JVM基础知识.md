# JVM

## 一些设置命令

```Bash

#常用的设置
-Xms：初始堆大小，JVM 启动的时候，给定堆空间大小。 
 
-Xmx：最大堆大小，JVM 运行过程中，如果初始堆空间不足的时候，最大可以扩展到多少。 
 
-Xmn：设置堆中年轻代大小。整个堆大小=年轻代大小+年老代大小+持久代大小。 
 
-XX:NewSize=n 设置年轻代初始化大小大小 
 
-XX:MaxNewSize=n 设置年轻代最大值
 
-XX:NewRatio=n 设置年轻代和年老代的比值。如: -XX:NewRatio=3，表示年轻代与年老代比值为 1：3，年轻代占整个年轻代+年老代和的 1/4 
 
-XX:SurvivorRatio=n 年轻代中 Eden 区与两个 Survivor 区的比值。注意 Survivor 区有两个。8表示两个Survivor :eden=2:8 ,即一个Survivor占年轻代的1/10，默认就为8
 
-Xss：设置每个线程的堆栈大小。JDK5后每个线程 Java 栈大小为 1M，以前每个线程堆栈大小为 256K。
 
-XX:ThreadStackSize=n 线程堆栈大小
 
-XX:PermSize=n 设置持久代初始值  
 
-XX:MaxPermSize=n 设置持久代大小
 
-XX:MaxTenuringThreshold=n 设置年轻带垃圾对象最大年龄。如果设置为 0 的话，则年轻代对象不经过 Survivor 区，直接进入年老代。
 
#下面是一些不常用的
 
-XX:LargePageSizeInBytes=n 设置堆内存的内存页大小
 
-XX:+UseFastAccessorMethods 优化原始类型的getter方法性能
 
-XX:+DisableExplicitGC 禁止在运行期显式地调用System.gc()，默认启用  
 
-XX:+AggressiveOpts 是否启用JVM开发团队最新的调优成果。例如编译优化，偏向锁，并行年老代收集等，jdk6纸之后默认启动
 
-XX:+UseBiasedLocking 是否启用偏向锁，JDK6默认启用  
 
-Xnoclassgc 是否禁用垃圾回收
 
-XX:+UseThreadPriorities 使用本地线程的优先级，默认启用  
 
```

# 优化

[JVM（Java虚拟机）优化大全和案例实战\_jvm虚拟优化\_lizhou828的博客-CSDN博客](https://blog.csdn.net/lizhou828/article/details/103478730)

# 垃圾回收器选择

串行收集器、——串行收集器只适用于小数据量的情况

并行收集器、

并发收集器。

默认情况下JDK5.0以前都是使用串行收集器，JDK5.0以后，JVM会根据当前系统配置进行智能判断。

串行收集器

-XX:+UseSerialGC：设置串行收集器。

### 并行收集器（吞吐量优先）

-XX:+UseParallelGC：设置为并行收集器。此配置仅对年轻代有效。即年轻代使用并行收集，而年老代仍使用串行收集。

-XX:ParallelGCThreads=20：配置并行收集器的线程数，即：同时有多少个线程一起进行垃圾回收。此值建议配置与CPU数目相等。

-XX:+UseParallelOldGC：配置年老代垃圾收集方式为并行收集。JDK6.0开始支持对年老代并行收集。

-XX:MaxGCPauseMillis=100：设置每次年轻代垃圾回收的最长时间（单位毫秒）。如果无法满足此时间，JVM会自动调整年轻代大小，以满足此时间。

-XX:+UseAdaptiveSizePolicy：设置此选项后，并行收集器会自动调整年轻代Eden区大小和Survivor区大小的比例，以达成目标系统规定的最低响应时间或者收集频率等指标。此参数建议在使用并行收集器时，一直打开。

### 并发收集器（响应时间优先）

-XX:+UseConcMarkSweepGC：即CMS收集，设置年老代为并发收集。CMS收集是JDK1.4后期版本开始引入的新GC算法。它的主要适合场景是对响应时间的重要性需求大于对吞吐量的需求，能够承受垃圾回收线程和应用线程共享CPU资源，并且应用中存在比较多的长生命周期对象。CMS收集的目标是尽量减少应用的暂停时间，减少Full GC发生的几率，利用和应用程序线程并发的垃圾回收线程来标记清除年老代内存。

-XX:+UseParNewGC：设置年轻代为并发收集。可与CMS收集同时使用。JDK5.0以上，JVM会根据系统配置自行设置，所以无需再设置此参数。

-XX:CMSFullGCsBeforeCompaction=0：由于并发收集器不对内存空间进行压缩和整理，所以运行一段时间并行收集以后会产生内存碎片，内存使用效率降低。此参数设置运行0次Full GC后对内存空间进行压缩和整理，即每次Full GC后立刻开始压缩和整理内存。

-XX:+UseCMSCompactAtFullCollection：打开内存空间的压缩和整理，在Full GC后执行。可能会影响性能，但可以消除内存碎片。

-XX:+CMSIncrementalMode：设置为增量收集模式。一般适用于单CPU情况。

-XX:CMSInitiatingOccupancyFraction=70：表示年老代内存空间使用到70%时就开始执行CMS收集，以确保年老代有足够的空间接纳来自年轻代的对象，避免Full GC的发生。

# 其它垃圾回收参数

-XX:+ScavengeBeforeFullGC：年轻代GC优于Full GC执行。

-XX:-DisableExplicitGC：不响应 System.gc() 代码。

-XX:+UseThreadPriorities：启用本地线程优先级API。即使 java.lang.Thread.setPriority() 生效，不启用则无效。

-XX:SoftRefLRUPolicyMSPerMB=0：软引用对象在最后一次被访问后能存活0毫秒（JVM默认为1000毫秒）。

-XX:TargetSurvivorRatio=90：允许90%的Survivor区被占用（JVM默认为50%）。提高对于Survivor区的使用率。

# 辅助信息参数设置

-XX:-CITime：打印消耗在JIT编译的时间。

-XX:ErrorFile=./hs_err_pid.log：保存错误日志或数据到指定文件中。

-XX:HeapDumpPath=./java_pid.hprof：指定Dump堆内存时的路径。

-XX:-HeapDumpOnOutOfMemoryError：当首次遭遇内存溢出时Dump出此时的堆内存。

-XX:OnError=";"：出现致命ERROR后运行自定义命令。

-XX:OnOutOfMemoryError=";"：当首次遭遇内存溢出时执行自定义命令。

-XX:-PrintClassHistogram：按下 Ctrl+Break 后打印堆内存中类实例的柱状信息，同JDK的 jmap -histo 命令。

-XX:-PrintConcurrentLocks：按下 Ctrl+Break 后打印线程栈中并发锁的相关信息，同JDK的 jstack -l 命令。

-XX:-PrintCompilation：当一个方法被编译时打印相关信息。

-XX:-PrintGC：每次GC时打印相关信息。

-XX:-PrintGCDetails：每次GC时打印详细信息。

-XX:-PrintGCTimeStamps：打印每次GC的时间戳。

-XX:-TraceClassLoading：跟踪类的加载信息。

-XX:-TraceClassLoadingPreorder：跟踪被引用到的所有类的加载信息。

-XX:-TraceClassResolution：跟踪常量池。

-XX:-TraceClassUnloading：跟踪类的卸载信息。

# 关于参数名称等

标准参数（-），所有JVM都必须支持这些参数的功能，而且向后兼容；例如：
-client——设置JVM使用Client模式，特点是启动速度比较快，但运行时性能和内存管理效率不高，通常用于客户端应用程序或开发调试；在32位环境下直接运行Java程序默认启用该模式。
-server——设置JVM使Server模式，特点是启动速度比较慢，但运行时性能和内存管理效率很高，适用于生产环境。在具有64位能力的JDK环境下默认启用该模式。

非标准参数（-X），默认JVM实现这些参数的功能，但是并不保证所有JVM实现都满足，且不保证向后兼容；

非稳定参数（-XX），此类参数各个JVM实现会有所不同，将来可能会不被支持，需要慎重使用；
