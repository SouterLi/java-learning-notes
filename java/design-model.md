# 设计模式
## 单例模式
### 实现
```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {
        // 私有构造函数，防止外部实例化
    }
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    // 线程安全的单例模式
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    // 双重检查锁定的单例模式
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
    // 静态内部类实现的单例模式
    public static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
    // 枚举实现的单例模式
    public enum SingletonEnum {
        INSTANCE;
        public void doSomething() {
            // 方法实现
        }
    }
    public static SingletonEnum getInstance() {
        return SingletonEnum.INSTANCE;
    }
}
```
### 单例模式的应用场景
- 当需要确保一个类只有一个实例时，例如配置管理器、日志记录器等。
- 当需要全局访问某个实例时，例如数据库连接池、线程池等。
- 当需要延迟加载实例时，例如在第一次使用时才创建实例。
### 单例模式的破坏
- 通过反序列化破坏单例模式：可以通过反序列化创建新的实例，解决方法是实现 `readResolve` 方法。
- 通过反射破坏单例模式：可以通过反射访问私有构造函数创建新的实例，解决方法是抛出异常或使用枚举类。

## 工厂模式
### 实现
```java
// 简单工厂模式
public class SimpleFactory {
    public static Product createProduct(String type) {
        if ("A".equals(type)) {
            return new ProductA();
        } else if ("B".equals(type)) {
            return new ProductB();
        }
        return null;
    }
}
// 工厂方法模式
public interface Factory {
    Product createProduct();
}
public class FactoryA implements Factory {
    @Override
    public Product createProduct() {
        return new ProductA();
    }
}
public class FactoryB implements Factory {
    @Override
    public Product createProduct() {
        return new ProductB();
    }
}
// 抽象工厂模式
public interface AbstractFactory {
    Product createProductA();
    Product createProductB();
}
public class ConcreteFactory implements AbstractFactory {
    @Override
    public Product createProductA() {
        return new ProductA();
    }
    @Override
    public Product createProductB() {
        return new ProductB();
    }
}
```
### 工厂模式的应用场景
- 当需要创建的对象具有相同的接口或抽象类时，例如图形界组件、数据库连接等。
- 当需要根据不同的条件创建不同的对象时，例如根据配置文件创建不同的日志记录器。
- 当需要将对象的创建与使用分离时，例如在依赖注入框架中使用工厂模式创建对象。

## 适配器模式
### 实现
```java
// 类适配器模式
public class Adapter extends Adaptee implements Target {
    @Override
    public void request() {
        specificRequest();
    }
}
// 对象适配器模式
public class Adapter implements Target {
    private Adaptee adaptee;
    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }
    @Override
    public void request() {
        adaptee.specificRequest();
    }
}
// 接口适配器模式
public abstract class AbstractAdapter implements Target {
    @Override
    public void request() {
        // 默认实现
    }
}
public class ConcreteAdapter extends AbstractAdapter {
    @Override
    public void request() {
        // 具体实现
    }
}
```
### 适配器模式的应用场景
- 当需要将一个接口转换为另一个接口时，例如将旧系统的接口适配到新系统的接口。
- 当需要使用一些现有的类，但它们的接口不符合当前系统的需求时，例如将第三方库的接口适配到自己的接口。
- 当需要在不修改现有代码的情况下扩展系统功能时，例如通过适配器模式添加新的功能或行为。
