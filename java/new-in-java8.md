# Java 新功能

## Java8 新特性概述
### Lambda 表达式
Lambda 表达式是 Java 8 引入的一种新语法，允许将函数作为参数传递给方法或将其赋值给变量。它使得函数式编程在 Java 中成为可能。
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
// 使用 Lambda 表达式过滤出长度大于 3 的名字
List<String> longNames = names.stream()
    .filter(name -> name.length() > 3)
    .collect(Collectors.toList());
System.out.println(longNames); // 输出: [Alice, Charlie]
```
### 函数式接口
函数式接口是只包含一个抽象方法的接口，可以使用 Lambda 表达式来实现。Java 8 提供了许多内置的函数式接口，如 `Predicate`、`Function`、`Consumer` 和 `Supplier` 等。
```java
@FunctionalInterface
public interface MyFunctionalInterface {
    void doSomething(String input);
}
MyFunctionalInterface myFunc = (input) -> System.out.println("Hello, " + input);
myFunc.doSomething("World"); // 输出: Hello, World
```
### Stream API
Stream API 是 Java 8 引入的用于处理集合的强大工具。它提供了对集合进行过滤、映射、排序和聚合等操作的能力。Stream API支持函数式编程风格，使得代码更加简洁和易读。
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
// 使用 Stream API 计算所有偶数的平方和
int sumOfSquares = numbers.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .reduce(0, Integer::sum);
System.out.println(sumOfSquares); // 输出: 20
```
### Optional 类
`Optional` 类是 Java 8 引入的一个容器类，用于表示可能为 null 的值。它提供了一种更安全的方式来处理可能为 null 的对象，避免了 NullPointerException 的发生。
```java
Optional<String> optionalName = Optional.ofNullable(getName());
optionalName.ifPresent(name -> System.out.println("Name: " + name));
String name = optionalName.orElse("Default Name");
System.out.println(name); // 输出: Name: [实际名称] 或 Default Name
```
### 方法引用
方法引用是 Java 8 引入的一种简化 Lambda 表达式的语法。它允许直接引用已有的方法，而不需要编写 Lambda 表达式的具体实现。
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
// 使用方法引用打印所有名字
names.forEach(System.out::println);
// 使用方法引用将名字转换为大写
List<String> upperCaseNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
System.out.println(upperCaseNames); // 输出: [ALICE, BOB, CHARLIE]
```
### 默认方法和静态方法
Java 8 允许在接口中定义默认方法和静态方法。默认方法可以在接口中提供默认实现，而静态方法可以直接在接口上调用。
```java
public interface MyInterface {
    default void defaultMethod() {
        System.out.println("This is a default method.");
    }
    static void staticMethod() {
        System.out.println("This is a static method.");
    }
}
public class MyClass implements MyInterface {
    // 可以覆盖默认方法
    @Override
    public void defaultMethod() {
        System.out.println("Overridden default method.");
    }
}
MyClass myClass = new MyClass();
myClass.defaultMethod(); // 输出: Overridden default method.
MyInterface.staticMethod(); // 输出: This is a static method.
```
### 新的日期和时间 API
Java 8 引入了新的日期和时间 API，位于 `java.time` 包中。这个 API 提供了更好的日期和时间处理能力，解决了旧的 `java.util.Date` 和 `java.util.Calendar` 的一些问题。
```java
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(1990, Month.JANUARY, 1);
Period age = Period.between(birthday, today);
System.out.println("Age: " + age.getYears() + " years"); // 输出: Age: [实际年龄] years
LocalTime now = LocalTime.now();
LocalTime noon = LocalTime.NOON;
Duration duration = Duration.between(now, noon);
System.out.println("Duration until noon: " + duration.toMinutes() + " minutes");
```
### 新的集合 API
Java 8 对集合 API 进行了增强，提供了许多新的方法来简化集合的操作，如 `forEach`、`removeIf`、`replaceAll` 等。
```java
List<String> names = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie"));
// 使用 forEach 遍历集合
names.forEach(name -> System.out.println("Name: " + name));
// 使用 removeIf 删除长度小于 4 的名字
names.removeIf(name -> name.length() < 4);
System.out.println(names); // 输出: [Alice, Charlie]
// 使用 replaceAll 将所有名字转换为大写
names.replaceAll(String::toUpperCase);
System.out.println(names); // 输出: [ALICE, CHARLIE]
```
### 其他改进
ConcurrentHashMap取消segment的概念，加锁的粒度变为node，将reentrantlock改为cas+synchronized  
HashMap的扩容机制改为使用链表+红黑树，链表长度超过8时转换为红黑树

