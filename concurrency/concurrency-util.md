# 并发工具类

## CompletableFuture
CompletableFuture是Java8引入的异步编程工具类。
- **核心功能**：创建异步任务。
  - supplyAsync()：有返回值任务
  - runAsync()：无返回值任务
- **高级功能**  
  - 链式组合能力：thenApply()，thenCompose()，thenCombine()
  - 异常处理
  - 多任务协调

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