# 垃圾回收
## 如何确认垃圾对象
在垃圾回收（Garbage Collection, GC）过程中，确认哪些对象是垃圾主要依赖于以下几种方法：
#### 1. 引用计数法（Reference Counting）
- 每个对象维护一个引用计数器，当有引用指向对象时，计数器加1，当引用失效时，计数器减1，当计数器为0时，对象被视为垃圾
- 缺点：无法处理循环引用的情况
#### 2. 可达性分析法（Reachability Analysis）
   大多数现代GC采用这种方法：
- 从GC Roots（一组必须活跃的引用）开始，通过引用链遍历所有可达对象，未被遍历到的对象被视为垃圾
### GC Roots通常包括：
- 虚拟机栈中引用的对象
- 方法区中类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI引用的对象
- 虚拟机内部引用（如系统类加载器等）

## CMS与G1的区别
#### 设计目标
CMS：以最短停顿时间（低延迟）为目标，适用于对响应时间敏感的应用（如Web服务）。  
G1：在可控停顿时间的基础上，兼顾吞吐量和内存碎片控制，适合大堆内存（>4GB）的应用。
#### 堆内存管理方式
CMS：  
物理分代：严格划分新生代（Eden、Survivor）和老年代，内存连续。  
算法：采用标记-清除（Mark-Sweep），会产生内存碎片，可能导致Full GC。  
G1：  
逻辑分代：将堆划分为多个Region（默认2048个），每个Region可以是Eden、Survivor或Old区，物理上不连续。  
算法：整体基于标记-整理（Mark-Compact），局部使用复制算法，减少碎片。  
#### 回收过程
| 阶段 | CMS | G1                   |
|---| --- |----------------------|
| 初始标记 | STW，标记GC Roots直接关联对象 | STW，同CMS但记录SATB快照    |
| 并发标记 | 与用户线程并行，记录SATB快照 | 并发标记，计算Region回收价值    |
| 重新标记 | STW，修正并发标记的变动（增量更新） | STW，处理SATB记录的引用变化    |
| 清理/回收 | 并发清理（产生浮动垃圾） | 按Region优先级回收（复制存活对象） |

## 常见GC异常的场景
1.频繁Full GC  
排查过程：分析GC日志，发现老年代上升快速，导出dump文件，使用MAT工具分析dump，发现有一个大对象频繁创建  
解决方案：降低这个大对象的创建频率。

## 强引用、软引用、弱引用、虚引用的GC影响
强引用：所有new出来的对象都是强引用，GC永不回收  
软引用：GC时如果内存不足，会被回收  
弱引用：GC时无论内存是否不足，都会被回收  
虚引用：主要用于资源清理跟踪

## 如何排查OOM问题
#### 1. 确认 OOM 类型
OOM 错误有多种类型，首先需明确具体错误类型（通过异常日志）：
- java.lang.OutOfMemoryError: Java heap space→ 堆内存不足（对象分配过多或内存泄漏）。
- java.lang.OutOfMemoryError: Metaspace→ 元空间（方法区）溢出（动态生成类过多，如反射、CGLIB）。
- java.lang.OutOfMemoryError: Unable to create new native thread→ 线程数超过系统限制（需检查线程泄漏或调整系统参数）。
- java.lang.OutOfMemoryError: GC overhead limit exceeded→ GC 回收效率过低（98%时间在GC，但堆内存仅释放2%）。
#### 2. 获取关键信息
(1) 错误日志：检查异常堆栈和 OOM 前的 GC 日志（需开启 JVM 参数）  
(2) 堆内存快照（Heap Dump）：在 OOM 时自动生成 Heap Dump。手动生成 Heap Dump。  
(3) 当前堆内存的配置情况：使用jmap -heap命令查看堆信息
#### 3. 使用工具内存泄漏分析（MAT/Eclipse Memory Analyzer）
   Java Visualvm软件