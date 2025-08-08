# Mybatis
## mybatis底层原理
动态代理：Mapper 接口通过代理类执行 SQL。
SQL 解析与执行：Executor + StatementHandler 处理 JDBC 操作。
结果映射：ResultSetHandler 转换查询结果。
缓存优化：一级缓存（会话级）和二级缓存（Mapper 级）提升性能。
动态 SQL：基于 OGNL 实现条件查询。
插件机制
## mybatis为什么可以防止sql注入？
因为MyBatis启用了预编译功能，在SQL执行前，会先将SQL发送给数据库进行编译；执行时，直接使用编译好的SQL，替换占位符“?”就可以了。因为SQL注入只能对编译过程起作用，所以这样的方式就很好地避免了SQL注入的问题。  
【底层实现原理】MyBatis是如何做到SQL预编译的呢？其实在框架底层，是JDBC中的PreparedStatement类在起作用，PreparedStatement是我们很熟悉的Statement的子类，它的对象包含了编译好的SQL语句。这种“准备好”的方式不仅能提高安全性，而且在多次执行同一个SQL时，能够提高效率。原因是SQL已编译好，再次执行时无需再编译。