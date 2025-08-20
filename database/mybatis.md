# Mybatis
## mybatis底层原理
- **动态代理**：Mapper 接口通过代理类执行 SQL。
- **SQL 解析与执行**：Executor + StatementHandler 处理 JDBC 操作。
- **结果映射**：ResultSetHandler 转换查询结果。
- **缓存优化**：一级缓存（会话级）和二级缓存（Mapper 级）提升性能。
- **动态 SQL**：基于 OGNL 实现条件查询。
- **插件机制**：通过拦截器实现 SQL 执行过程的增强。
### Mapper接口工作原理
   通过JDK动态代理生成代理对象，根据"全限定名+方法名"定位对应的SQL语句并执行。
   接口方法不能重载，因为MyBatis通过方法名唯一标识SQL映射。
### mybatis 是如何实现插件机制的？
MyBatis 的插件机制是通过拦截器实现的。拦截器可以在执行 SQL 之前、执行 SQL 之后、查询结果映射之前等多个阶段对 SQL 执行过程进行拦截和修改。  
具体实现步骤如下：
1. **定义拦截器接口**：实现 `Interceptor` 接口，重写 `intercept` 方法。
2. **配置拦截器**：在 MyBatis 的配置文件中注册拦截器，指定要拦截的目标方法。
3. **拦截器链**：MyBatis 在执行 SQL 时，会按照配置的顺序依次调用拦截器的 `intercept` 方法，可以在这个方法中对 SQL 进行修改或增强功能。
4. **插件注入**：通过 `Plugin.wrap` 方法将目标对象包装成拦截器对象，使得 MyBatis 在执行 SQL 时能够调用拦截器的逻辑。
### mybatis的缓存机制
MyBatis 的缓存机制分为一级缓存和二级缓存：
1. **一级缓存**：每个 SqlSession 都有自己的一级缓存，默认开启。查询结果会存储在 SqlSession 的缓存中，直到 SqlSession 关闭或清空。一级缓存的作用域是 SqlSession，适用于同一会话内的多次查询。
2. **二级缓存**：二级缓存是跨 SqlSession 的缓存，可以在多个 SqlSession 之间共享。二级缓存需要在 Mapper 接口上配置，并且需要在 MyBatis 的配置文件中开启。二级缓存可以使用不同的缓存实现，如 Ehcache、Redis 等。二级缓存的作用域是 Mapper，适用于跨会话的查询。

## mybatis为什么可以防止sql注入？
因为MyBatis启用了预编译功能，在SQL执行前，会先将SQL发送给数据库进行编译；
执行时，直接使用编译好的SQL，替换占位符“?”就可以了。因为SQL注入只能对编译过程起作用，所以这样的方式就很好地避免了SQL注入的问题。  
### MyBatis是如何做到SQL预编译的呢？
其实在框架底层，是JDBC中的PreparedStatement类在起作用，PreparedStatement是我们很熟悉的Statement的子类，它的对象包含了编译好的SQL语句。
这种“准备好”的方式不仅能提高安全性，而且在多次执行同一个SQL时，能够提高效率。
原因是SQL已编译好，再次执行时无需再编译。

