# Java基础知识总结
## Java8 新功能
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
### 其他改进
ConcurrentHashMap取消segment的概念，加锁的粒度变为node，将reentrantlock改为cas+synchronized  
HashMap的扩容机制改为使用链表+红黑树，链表长度超过8时转换为红黑树

## 面向对象
### object类有哪些常用方法？
toString方法，如果没有重写，此方法打印的是对象的地址  
equals方法，比较对象是否相等  
hashcode方法，获取对象的hashcode  
clone方法，克隆对象  
notify方法，唤醒睡眠中的线程  
notifyAll方法，唤醒所有睡眠中的线程  
wait方法，线程等待方法；  
finalize方法，垃圾回收相关
### 泛型
#### 泛型的好处
1.类型安全：编译时检查类型，避免运行时类型转换异常  
2.代码复用：可以编写通用的代码，处理多种类型  
3.可读性：代码更清晰，避免了强制类型转换  
4.性能优化：减少了类型转换的开销，提高了性能  
#### 泛型的使用场景有哪些？  
1.集合容器（最常用），让集合只能存放指定类型，避免类型混乱
2.工具方法，写一个方法能处理多种类型
3.接口/类设计，设计通用的组件
4.避免重复代码，当多个方法逻辑相同只是类型不同时，可以用泛型代替入参
#### 泛型擦除
泛型擦除是指在编译时，Java编译器会将泛型类型转换为原始类型（raw type），从而使得泛型信息在运行时不可用。这是为了保持向后兼容性，因为在Java 1.4之前没有泛型。
比如
```java
List<String> list = new ArrayList<>();
```
在编译时会被转换为
```java
List list = new ArrayList();
```

## 反射
### 反射的定义
反射是指程序在运行时可以获取自身的信息，并且可以操作这些信息的能力。Java 反射机制允许程序在运行时查询类的信息、创建对象、调用方法以及访问字段等。
### 反射常用的方法
#### 获取类的信息
- `Class.forName("类的全限定名")`：通过类的全限定名获取 Class 对象。
- `类名.class`：通过类名直接获取 Class 对象。
- `对象.getClass()`：通过对象实例获取 Class 对象。
#### 创建对象
- `Class.newInstance()`：通过 Class 对象创建一个新的实例（需要类有无参构造方法）。
- `Constructor.newInstance(参数)`：通过 Constructor 对象创建一个新的实例，可以传入参数。
#### 访问字段
- `Field.get(Object obj)`：获取指定对象的字段值。
- `Field.set(Object obj, Object value)`：设置指定对象的字段值。
- `Field.getType()`：获取字段的类型。
#### 调用方法
- `Method.invoke(Object obj, Object... args)`：调用指定对象的方法，可以传入参数。
- `Method.getParameterTypes()`：获取方法的参数类型。
- `Method.getReturnType()`：获取方法的返回类型。
### 反射的原理
Java 反射机制是通过 `java.lang.reflect` 包中的类来实现的。每个类在加载时，JVM 会为其创建一个 `Class` 对象，这个对象包含了类的结构信息（如字段、方法、构造方法等）。通过这个 `Class` 对象，程序可以在运行时动态地获取和操作类的信息。
反射的实现依赖于 JVM 的类加载机制，当一个类被加载时，JVM 会将类的字节码转换为 `Class` 对象，并将其存储在方法区中。反射机制通过这些 `Class` 对象提供了对类的动态访问能力。
### 反射的性能问题
反射机制虽然强大，但也存在性能问题。  
由于反射需要在运行时解析类的信息，因此相较于直接调用方法或访问字段，反射的性能开销较大。此外，反射操作通常会绕过 Java 的访问控制检查，这可能导致安全问题。
因此，在性能敏感的场景中，应尽量避免使用反射，或者在使用反射时进行性能优化，例如缓存反射结果、减少反射调用次数等。