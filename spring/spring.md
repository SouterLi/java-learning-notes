# Spring
## Spring的启动过程
1. **创建Spring容器**：加载配置文件（如`applicationContext.xml`或Java配置类），创建`ApplicationContext`实例。
2. **加载Bean定义**：解析配置文件或注解，加载所有的Bean定义到容器中。
3. **实例化Bean**：根据Bean定义创建Bean实例。
4. **依赖注入**：根据Bean定义中的依赖关系，将依赖的Bean注入到当前Bean中。
5. **初始化Bean**：调用Bean的初始化方法（如`@PostConstruct`注解的方法或实现`InitializingBean`接口的`afterPropertiesSet`方法）。
6. **容器就绪**：所有Bean都已初始化完成，容器就绪，可以处理请求或执行任务。
7. **销毁Bean**：当容器关闭时，调用Bean的销毁方法（如`@PreDestroy`注解的方法或实现`DisposableBean`接口的`destroy`方法）。

## Spring常用注解
- `@Component`：标记一个类为Spring组件，自动扫描并注册到容器中。
- `@Service`：标记一个类为服务层组件，通常用于业务逻辑处理。
- `@Repository`：标记一个类为数据访问层组件，通常用于数据库操作。
- `@Controller`：标记一个类为控制器组件，通常用于处理Web请求。
- `@Autowired`：自动注入依赖的Bean。
- `@Qualifier`：指定注入的Bean名称，用于解决自动注入时的歧义。
- `@Value`：注入配置文件中的属性值。
- `@Configuration`：标记一个类为配置类，通常用于定义Bean的创建和依赖关系。
- `@Bean`：在配置类中定义一个Bean，用于创建和配置Bean实例。
- `@PostConstruct`：在Bean初始化后执行的方法。
- `@PreDestroy`：在Bean销毁前执行的方法。
- `@Scope`：定义Bean的作用域，如`singleton`（单例）、`prototype`（原型）、`request`（请求）、`session`（会话）。
- `@Lazy`：延迟加载Bean，只有在真正需要时才创建。
- `@Conditional`：根据特定条件决定是否加载Bean。
- `@Profile`：根据不同的环境加载不同的配置。
- `@Transactional`：声明事务，通常用于服务层方法。
- `@Aspect`：定义一个切面，用于AOP编程。
- `@Pointcut`：定义切点，用于指定哪些方法需要被拦截。
- `@Before`、`@After`、`@Around`：定义切面方法的执行时机，分别表示在方法执行前、后和环绕执行。

## Spring的AOP
### 什么是AOP？



## Spring中Bean的生命周期
1. **实例化**：根据Bean定义创建Bean实例。
2. **填充属性**：将依赖的Bean注入到当前Bean中
3. **调用初始化方法**：执行`@PostConstruct`注解的方法或实现`InitializingBean`接口的`afterPropertiesSet`方法。
4. **Bean就绪**：Bean已完全初始化，可以处理请求或执行任务。
5. **销毁**：当容器关闭时，调用`@PreDestroy`注解的方法或实现`DisposableBean`接口的`destroy`方法。

## spring在bean初始化的时候进行自定义操作怎么处理，用哪个注解？
在Spring中，可以使用`@PostConstruct`注解来定义Bean初始化时执行的自定义操作。这个注解可以标记在一个方法上，该方法会在Bean实例化并填充属性后自动调用。
```java
import javax.annotation.PostConstruct;
import org.springframework.stereotype.Component;
@Component
public class MyBean {
    @PostConstruct
    public void init() {
        // 自定义初始化操作
        System.out.println("MyBean has been initialized.");
    }
}
```

## Spring如何解决Bean循环依赖的问题？
Spring通过三级缓存机制来解决Bean的循环依赖问题。三级缓存包括：
1. **一级缓存（singletonObjects）**：存放完全初始化好的单例Bean
2. **二级缓存（earlySingletonObjects）**：存放正在创建中的单例Bean的早期引用
3. **三级缓存（singletonFactories）**：存放Bean的工厂对象，用于创建Bean的早期引用

## Spring
ssm框架-Spring框架体系结构
Bean的生命周期
Spring关键技术原理
Spring框架配置
核心组件
IOC机制，源码分析
BeanDefinition深度理解
利用三级缓存解决循环依赖问题
AOP使用，源码
寻找入口方式
获取增强器过程
匹配获取代理对象
创建代理类
代理实例调用invoke
spring事务
springMVC执行流程
springMVC初始化过程
springboot框架
自动装配原理