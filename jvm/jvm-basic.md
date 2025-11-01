# JVM知识总结

## Java字节码结构
Java字节码是Java源代码编译后的中间表示形式，存储在`.class`文件中。Java字节码由一系列指令组成，这些指令被Java虚拟机（JVM）解释执行。Java字节码结构主要包括以下几个部分：
```text
Java字节码文件的结构：
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    u2             constant_pool_count;
    cp_info        constant_pool[constant_pool_count-1];
    u2             access_flags;
    u2             this_class;
    u2             super_class;
    u2             interfaces_count;
    u2             interfaces[interfaces_count];
    u2             fields_count;
    field_info     fields[fields_count];
    u2             methods_count;
    method_info    methods[methods_count];
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```
1. **魔数（Magic Number）**：每个`.class`文件的开头都有一个固定的魔数`0xCAFEBABE`，用于标识这是一个Java字节码文件。
2. **版本号（Version Number）**：紧接着魔数之后的是版本号，表示该字节码文件所兼容的Java版本。
3. **常量池（Constant Pool）**：常量池是一个表格，存储了类中的各种常量，如字符串、类名、方法名等。JVM在运行时会使用这些常量。
4. **访问标志（Access Flags）**：用于描述类或接口的访问权限和属性，如是否为公共类、抽象类等。
5. **类索引（This Class）**：指向常量池中表示该类的条目。
6. **父类索引（Super Class）**：指向常量池中表示该类的父类的条目。
7. **接口表（Interfaces）**：列出该类实现的所有接口。
8. **字段表（Fields）**：描述类中的字段信息，包括字段名、类型和访问权限等。
9. **方法表（Methods）**：描述类中的方法信息，包括方法名、参数类型、返回类型和访问权限等。
10. **属性表（Attributes）**：存储类、字段和方法的附加信息，如源文件名、调试信息等。