# jvm常见面试题

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

## jvm
JVM性能调优
深入JVM内存区域
内存区域详解
堆、栈内存区域对象
方法的调用调出栈
jvm参数设定
JDK源码深入内存区域
JVM执行子系统
Class文件内容深入及本质
理解并掌握常见的字节码指令
双亲委派的类加载机制
基于方法调用，栈帧
垃圾回收机制
对象存活及生命周期、强弱虚引用在Apring框架中的应用
GC常见算法、分代垃圾回收
GC日志排查、内存泄漏和内存溢出分析
内存分配和回收策略深入