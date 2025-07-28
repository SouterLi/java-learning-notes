# Spring Boot
## Spring Boot自动装配原理
基于 @Conditional 和 spring.factories，按需加载配置类。
启动应用时，@SpringBootApplication 内部的 @EnableAutoConfiguration注解触发自动配置。
加载spring.factories，读取所有自动配置类。
然后检查 @Conditional 条件，决定是否加载该配置类。
符合条件的配置类会将 Bean注册到容器中去。
## Spring boot如何优化启动速度
1. **懒加载**：默认情况下，Spring Boot会在应用启动时加载所有的Bean。通过设置`@Lazy`注解，可以延迟加载某些Bean，直到真正需要时才创建。
2. **条件装配**：使用`@Conditional`注解，可以根据特定条件来决定是否加载某个Bean。这样可以避免加载不必要的Bean，从而提高启动速度。
3. **排除不必要的自动配置**：通过`@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})`等方式，可以排除不需要的自动配置类，减少启动时的Bean创建。
4. **使用Profile**：通过`@Profile`注解，可以根据不同的环境加载不同的配置，从而避免加载不必要的Bean。
5. **使用Spring Boot DevTools**：在开发环境中，可以使用Spring Boot DevTools来加快重启速度。它提供了自动重启、热加载等功能，可以减少开发过程中的等待时间。
6. **使用Spring Boot的启动器**：
   Spring Boot提供了许多启动器（Starter），可以快速引入常用的依赖和配置。使用启动器可以减少手动配置的工作量，从而提高启动速度。
7. **使用Spring Boot的缓存机制**：Spring Boot提供了多种缓存机制，如Ehcache、Redis等，可以在应用启动时缓存一些常用的数据，减少后续请求的处理时间。
8. **优化依赖管理**：通过使用Maven或Gradle等构建工具，可以优化依赖管理，避免引入不必要的依赖，从而减少启动时的类加载时间。
9. **使用Spring Boot的异步处理**：通过使用`@Async`注解，可以将某些操作异步执行，从而提高应用的响应速度和启动速度。
10. **使用Spring Boot的配置文件**：
    Spring Boot允许使用`application.properties`或`application.yml`文件来配置应用。通过合理配置，可以减少不必要的Bean创建和初始化，从而提高启动速度。
