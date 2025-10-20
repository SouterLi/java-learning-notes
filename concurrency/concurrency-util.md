# 并发工具类

## AQS
AQS，抽象队列同步器，它是实现ReentrantLock和CountDownLatch的关键。
AQS维护了一个资源和资源状态，还有一个FIFO的队列，资源状态是使用volatile关键字修饰的，为了保证资源的可见性。
### 实现ReentrantLock和CountDownLatch
#### ReentrantLock
以ReentrantLock为例，state初始化为0，表示未锁定状态。A线程lock()时，会调用tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这就是可重入的概念。但要注意，获取多少次就要释放多么次，这样才能保证state是能回到零态的。
#### CountDownLatch
再以CountDownLatch以例，任务分为N个子线程去执行，state也初始化为N（注意N要与线程个数一致）。这N个子线程是并行执行的，每个子线程执行完后countDown()一次，state会CAS减1。等到所有子线程都执行完后(即state=0)，会unpark()主调用线程，然后主调用线程就会从await()函数返回，继续后余动作。

## CompletableFuture
CompletableFuture是Java8引入的异步编程工具类。
- **核心功能**：创建异步任务。
  - supplyAsync()：有返回值任务
  - runAsync()：无返回值任务
- **高级功能**  
  - 链式组合能力：thenApply()，thenCompose()，thenCombine()
  - 异常处理
  - 多任务协调
### FutureTask vs CompletableFuture
| 特性               | FutureTask                      | CompletableFuture                 |
|------------------|--------------------------------|----------------------------------|
| 异步任务创建         | 只能通过构造函数创建               | 提供多种静态方法（supplyAsync, runAsync）创建异步任务 |
| 链式调用           | 不支持                           | 支持（thenApply, thenCompose等）          |
| 异常处理           | 需要手动捕获异常                   | 提供异常处理方法（exceptionally, handle等）   |
| 多任务协调         | 需要手动管理多个FutureTask          | 提供多任务协调方法（allOf, anyOf等）        |
| 可读性和简洁性       | 代码较为冗长                     | 代码更简洁，易读性更高                 |

### 什么是异步任务？
如下代码，我们使用CompletableFuture.supplyAsync()提交了一个1秒后返回Hello的任务，
```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    Thread.sleep(1000); // 模拟耗时操作
    return "Hello";
});
System.out.println("主线程继续执行...");  // 这里会立即打印
future.thenAccept(result -> 
    System.out.println("异步处理结果: " + result)
);
System.out.println("程序结束");
```
由于这个是非阻塞任务，主线程不会被阻塞，只有future.thenAccept这部分会被阻塞。所以最终打印结果为：
```text
主线程继续执行...
程序结束
异步处理结果: Hello  // 1秒后打印
```

## CountDownLatch
### 核心概念
CounDownLatch是一个带计数器的并发控制器。它的作用是主线程等待多个其他线程，当所有其他线程都完成后，主线程才会被唤醒，否则就阻塞。  
CountDownLatch初始化的时候会设置一个计数值，每当一个其他线程完成的时候，调用一次countDown()方法，计数器减一，当计数器减到0的时候，await()方法停止被阻塞，继续向后执行。
### 实现原理
CountDownLatch基于AQS实现。  
初始化一个AQS，state为计数器的值，每次线程结束，调用releaseShared(1)方法，state-1，当state归零的时候唤醒所有等待线程。
### 注意事项
务必在 finally 块中调用 countDown() 防止异常导致计数器无法归零  
使用带超时的 await() 方法，避免长时间阻塞

## Fork/Join原理
Fork/Join框架是Java 7引入的一种并行计算框架，基于分治算法思想，适用于大规模数据处理和计算密集型任务。它通过将任务拆分为更小的子任务，并行执行这些子任务，最后合并结果来提高性能。
### 核心组件
1. **ForkJoinPool**：线程池，管理和调度Fork/Join任务
2. **ForkJoinTask**：抽象类，表示一个可以被Fork/Join框架执行的任务
3. **RecursiveTask<V>**：ForkJoinTask的子类，表示有返回值的任务
4. **RecursiveAction**：ForkJoinTask的子类，表示无返回值的任务
### 工作原理
1. **任务拆分（Fork）**：将大任务拆分为多个小任务，直到任务足够小，可以直接计算。
2. **任务执行**：将拆分后的子任务提交给ForkJoinPool进行并行执行。
3. **结果合并（Join）**：等待所有子任务完成，并将它们的结果合并为最终结果。
### 工作窃取算法
Fork/Join框架采用工作窃取算法（Work Stealing）来提高线程利用率。当一个线程完成了自己的任务队列后，会尝试从其他线程的任务队列中“窃取”任务来执行，从而减少线程的空闲时间，提高整体性能。