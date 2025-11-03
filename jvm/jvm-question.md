# jvm常见面试题

## jvm有哪些可以设置的参数？
JVM参数主要分为三类：标准参数、非标准参数和调试参数
1. **标准参数**（-X开头）：这些参数在所有JVM实现中都是通用的，例如：
   - `-Xms`：设置JVM初始堆内存大小
   - `-Xmx`：设置JVM最大堆内存大小
   - `-Xss`：设置每个线程的栈大小
2. **非标准参数**（-XX开头）：这些参数是特定于某个JVM实现的，通常用于调优JVM性能，例如：
   - `-XX:+UseG1GC`：启用G1垃圾收集器
   - `-XX:MaxMetaspaceSize`：设置元空间的最大大小
   - `-XX:+HeapDumpOnOutOfMemoryError`：在发生OOM时生成堆转储快照

## 1.Java对象一定在堆中分配吗？
不一定。
Java对象通常在堆内存中分配，但有一些例外情况：
1. **栈上分配**：在某些情况下，JVM可以将对象分配在栈上，例如逃逸分析（Escape Analysis）确定对象不会逃逸出方法时，JVM可以将其分配在栈上以提高性能。
2. **本地方法栈**：使用JNI（Java Native Interface）调用的本地方法中创建的对象可能会在本地方法栈中分配。
3. **方法区**：类的静态变量和常量池中的对象会存储在方法区（在Java 8及更高版本中，方法区被称为元空间）。 

## 如何排查OOM问题?
### 1. 立即保留现场证据
1. 保存dump文件
``` bash
# 方式1：在启动参数中预先添加，自动在OOM时生成dump
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid<pid>.hprof
# 方式2：OOM发生后，立即通过命令在线生成（如果进程还未挂）
jmap -dump:format=b,file=heap.hprof <pid>
```
2. 保存GC日志和JVM参数
### 2. 确认 OOM 类型
查看报错日志，确认OOM类型。
- Java heap space→ 堆内存不足（对象分配过多或内存泄漏）。
- Metaspace→ 元空间（方法区）溢出（动态生成类过多，如反射、CGLIB）。
- Unable to create new native thread→ 线程数超过系统限制（需检查线程泄漏或调整系统参数）。

### 3. 使用工具内存泄漏分析
使用MAT (Eclipse Memory Analyzer) 分析 .hprof 文件：
- 打开Leak Suspects Report：MAT会自动生成报告，提示可能的内存泄漏点。
- 查看Dominator Tree：列出最大的对象实例，找到占用内存最大的对象。
- 查看Path to GC Roots：对可疑对象，查看其到GC Roots的引用链。这是找到“罪魁祸首”的关键！排除弱引用（weak reference），找到那些本不该存在的强引用（strong reference）。

## 常见的JVM分析工具有哪些？
### 1. JDK自带工具
- jstat：监控JVM内存、GC等性能指标，可以查看各代内存使用情况和GC统计信息
- jmap：生成堆内存快照（Heap Dump）
- jstack：生成线程堆栈快照，分析线程状态
- jinfo：查看和修改JVM参数
### 2. 第三方工具
- VisualVM：图形化监控和分析JVM性能
- Eclipse MAT：分析Heap Dump，查找内存泄漏
- GCViewer：分析GC日志，优化GC性能

## 常见GC异常的场景
#### 1.频繁Full GC
现象：Full GC由几天一次变为几分钟一次，每次Full GC后内存释放很少
排查过程：使用jstat分析JVM内存和GC日志，发现老年代上升快速，导出dump文件，使用Eclipse MAT分析dump文件，查找内存泄露源头  
解决方案：修复内存泄漏的代码
#### 2. 频繁Full GC（2）
现象：Full GC频繁，但是发现每次Full GC后内存释放很多，但是很快又满了  
排查过程：首先这种情况，先做几个假设，第一个是因为新生代太小，导致对象晋升到老年代过快，很多短周期的对象进入老年代，导致老年代快速上升；第二个假设是老年代本身就不够大，导致对象晋升后很快就满了。  
使用jinfo查看JVM参数，确认新生代和老年代的大小，发现新生代比较小，老年代也不大  
解决方案：先尝试增大新生代的大小，观察是否能缓解问题，如果不行，再尝试增大老年代的大小  

## 讲一个你生产上遇到的jvm相关的一次事故，以及你是如何处理解决这次事故的？
### 事故一
有一次我们生产环境的一个服务突然变得非常慢，响应时间从几十毫秒变成了几秒钟，严重影响了用户体验。我们怀疑是JVM内存问题导致的，于是我们立刻使用jstat命令监控JVM的内存使用情况，发现老年代的内存使用率已经达到了100%，并且频繁发生Full GC。接着我们使用jmap命令生成了堆内存快照，并使用Eclipse MAT工具分析了堆内存快照。通过分析，我们发现有一个缓存对象占用了大量的内存，并且这个缓存对象没有被及时清理，导致内存泄漏。我们立即修复了代码中的缓存清理逻辑，并且在代码中添加了缓存的过期时间，防止类似的问题再次发生。
为了防止类似的问题再次发生，我们还优化了JVM的内存参数，增加了老年代的内存大小，并且定期监控JVM的内存使用情况，确保系统的稳定运行。
### 事故二
#### 现象
延迟飙升：P99延迟从50ms飙升到10秒以上。
GC异常：JVM监控显示，Full GC次数疯狂上涨，从每小时几次变成每分钟几次，且每次Full GC后老年代内存几乎不释放。
#### 处理流程
1.快速重启服务
2.重启之前保留现场，生成dump文件
3.使用Eclipse MAT进行分析，发现有一个Map占用的非常大的内存，查看Path to GC Roots（查看GC根路径），发现Map还一个static变量引用着。
#### 根因定位
新增了一个静态Map作为本地缓存，但是这个变量没有设置大小限制，也没有设置过期时间，而且存储的value是整个文件的所有信息。
垃圾回收判断不能回收，进入老年代，迅速占满老年代，导致频繁的full GC，导致应用线程被占用，服务延迟升高。

## JVM常见的参数
- `-Xms`：设置JVM初始堆内存大小
- `-Xmx`：设置JVM最大堆内存大小
- `-Xss`：设置每个线程的栈大小
- `-XX:+UseG1GC`：启用G1垃圾收集器
- `-XX:MaxMetaspaceSize`：设置元空间的最大大小
- `-XX:+HeapDumpOnOutOfMemoryError`：在发生OOM时生成堆转储快照
- `-XX:HeapDumpPath`：指定堆转储文件的路径
- `-XX:+PrintGCDetails`：打印GC的详细信息
- `-XX:+PrintGCDateStamps`：打印GC的时间戳
- `-XX:+UseConcMarkSweepGC`：启用并发标记清除垃圾收集器
- `-XX:NewRatio`：设置新生代与老年代的比例
- `-XX:SurvivorRatio`：设置Eden区与Survivor区的比例
