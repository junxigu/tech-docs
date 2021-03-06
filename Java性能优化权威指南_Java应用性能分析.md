# Java应用性能分析

Java应用的性能分析主要包括 方法分析，锁竞争分析 和 内存分析

主要的分析工具包括：Performance Analyzer, VisualVM

## 方法分析

方法分析工具抓取到的信息中，一般查看的数据包括：  
方法占用的用户态CPU时间，独占用户态CPU时间  
方法间的调用关系  

通过分析方法的占用时间来定位导致系统瓶颈的嫌疑方法  
例如，某些方法使用了运行与系统态的I/O，消耗了大量时间，可以考虑用缓冲或更高效I/O库来进行优化  
例如，某些方法因为锁竞争浪费大量CPU时间，考虑考虑用更高效的并发数据结构，或消除竞争等方法来优化

## 内存分析

通过分析工具可以抓取到，内存中各种对象的内存占用量，可以通过分析某些占用内存较多对象存活的代数来定位是否存在内存泄漏，并能定位创建这些对象的方法来定位内存泄漏的代码

JVM在发生OOM时会生成一个堆转存，这是一个内存的快照，可以通过上述工具来对当时的内存进行分析，找出内存泄漏的地方

通过内存分析能还可以发现是否存在因不合理的初始设置大量创建没用的小对象的集合数据结构，通过优化初始设置来优化这些集合数据结构的使用

## 性能优化机会

对Java应用性能优化的机会主要有3种   

1. 使用更高效的算法
2. 减少锁竞争
3. 为算法生成更有效率的代码

当通过性能分析找到影响应用性能的根源时，主要通过这3种方法来优化