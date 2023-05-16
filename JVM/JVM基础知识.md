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
