# Java 异常处理
## 异常分类
Java 中的异常分为两大类：
1. **检查异常（Checked Exception）**：在编译时必须处理的异常，如 `IOException`、`SQLException` 等。必须使用 `try-catch` 块或在方法签名中声明 `throws`。
2. **运行时异常（Runtime Exception）**：在运行时可能发生的异常，如 `NullPointerException`、`ArrayIndexOutOfBoundsException` 等。这些异常通常是程序错误导致的，可以选择不处理。

## 异常处理
1. try-catch捕获异常并处理
2. throws声明异常

## new Throwable()