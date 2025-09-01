# 线程知识总结
## 创建线程的几种方法
1.继承 Thread 类，重写 run 方法
```java
// 1. 继承Thread类
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("通过继承Thread创建线程: " + Thread.currentThread().getName());
    }
}
```
2. 实现 RunAble 接口
```java
public class Main {
    public static void main(String[] args) {
        // 使用Lambda表达式（Java 8+）
        Thread lambdaThread = new Thread(() -> {
            System.out.println("通过Lambda表达式创建线程: " + Thread.currentThread().getName());
        });
        lambdaThread.start();
    }
}
```
3. 线程池
```java
// 创建线程池
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    corePoolSize,
    maxPoolSize,
    keepAliveTime,
    unit,
    workQueue,
    Executors.defaultThreadFactory(),
    new ThreadPoolExecutor.AbortPolicy()
);
```

## 线程池
### 为什么使用线程池？
线程池（Thread Pool）是一种管理和复用线程的机制，主要解决以下问题：
1. 降低线程创建和销毁的开销
2. 控制并发线程数量，防止资源耗尽
3. 提高响应速度：任务到达时，可直接使用 空闲线程 执行，无需等待线程创建。
4. 统一管理任务：提供任务队列、拒绝策略等功能，增强系统的可控性。

### 线程池的核心参数（以 ThreadPoolExecutor 为例）
线程池的行为由以下 7 个核心参数决定： 
1. **corePoolSize**：核心线程数（长期存活的线程，即使空闲也不会被回收）
2. **maximumPoolSize**：最大线程数（包括核心线程和非核心线程）
3. **keepAliveTime**：非核心线程的空闲存活时间（超时后回收）60L（单位：秒）
4. **unitKeepAliveTime**：的时间单位TimeUnit.SECONDS
5. **workQueue**：任务队列（存放待执行的任务）new LinkedBlockingQueue<>()
6. **threadFactory**：线程工厂（用于创建线程，可自定义线程名、优先级等）Executors.defaultThreadFactory()
7. **handler**：拒绝策略（当任务数超过队列容量 + 最大线程数时的处理方式）new ThreadPoolExecutor.Abort

### 线程池的拒绝策略
当任务无法处理时，线程池会执行拒绝策略（RejectedExecutionHandler），常见策略：
- **AbortPolicy**：（默认）直接抛出RejectedExecutionException，阻止系统运行。
- **CallerRunsPolicy**：让提交任务的线程自己执行该任务（降低提交速度）。
- **DiscardPolicy**：静默丢弃任务，不抛异常。
- **DiscardOldestPolicy**：丢弃队列中最旧的任务，然后重新尝试提交当前任务。

## 原子类
是基于CAS原理实现的线程安全的工具类，覆盖了基本类型，数组引用等类型，无锁设计，性能比synchronized快10倍。