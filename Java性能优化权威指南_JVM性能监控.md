# JVM性能监控

JVM的性能监控主要包括3种监控：垃圾回收、JIT编译 和 类加载

## 监控JVM垃圾回收

**命令行工具**

通过用命令行参数来打印出垃圾回收的一些信息，通过这些信息来监控垃圾回收

获取垃圾收集信息的相关命令：  -verbose:gc 输出垃圾收集信息  -XX:+PrintGCDetails 输出GC详细信息  -XX:+PrintGCTimeStamps 输出时间戳  -XX:+PrintGCDateStamps 输出日期  -Xloggc:<filename> 输出到文件  -XX:+PrintGCApplicationConcurrentTIme 报告application在安全点之间的运行时间  +XX:+PrintGCApplicationStoppedTime 报告application线程被阻塞的时间  
从垃圾收集输出的日志里需要获得信息包括： 
1. 所使用的垃圾收集器  
2. Java堆大小
3. 新生代和老年代大小
4. 永久代大小
5. Minor GC持续时间
6. Minor GC的频率
7. Minor GC的空间回收量
8. Major GC的持续时间
9. Major GC的频率
10. 每个并发回收周期内的空间回收量
11. 垃圾回收前后Java堆的占用量
12. 垃圾回收前后新生代和老年代的占用量
13. 垃圾回收前后永久代的占用量
14. 是否老年代或永久代的占用触发Major GC
15. 应用是否显式触发垃圾回收

对收集到的垃圾收集信息可以用GCHisto来进行离线分析

**图形化工具**

JConsole，VisualGC和VisualVM

## 监控JIT编译

JIT编译器会对运行次数足够多的代码进行优化，对于优化不当的代码会进行逆优化甚至重新优化

当需要监控优化，逆优化和重新优化的信息时可用命令行参数  
-XX:+PrintCompilation

## 监控类加载

当类加载到足够多的时候会触发垃圾回收，通过监控类加载信息能为优化提供信息

可以使用图形化工具来查看类的加载量：JConsole，VisualGC和VisualVM

可通过垃圾回收的日志信息来查看类的卸载

## 监控Java应用

监控应用的常用方法是查看应用的输出日志  
当应用使用了内置的MBean时，可以使用兼容的JMX工具来监控这些MBean

若要快速地定位应用的锁竞争信息，可以通过命令工具jstack来获取线程的转储信息