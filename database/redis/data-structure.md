# Redis的数据结构
### String
字符串类型，实现方式是简单动态字符串。  
**简单动态字符串**：由三部分组成，len记录字符串的长度，buf[]指向实际存储字符串的内存地址，free指向剩余的内存空间。
### Hash
Hash类型，类似于Java中的Map，存储键值对。  
当数据量较少时，使用ziplist实现；当数据量较大时，使用hashtable实现。  
**ziplist**：是一种压缩列表，字段和值交替排列，使用连续的内存空间存储键值对。
### List
列表类型，类似于Java中的List，存储有序的字符串。实现原理是双向链表。
### Set
集合类型，类似于Java中的Set，无序，不可重复。
### SortedSet / ZSet
类似Set，但是有序。  
使用hashMap和跳表保证数据的存储和有序，HashMap里放的是成员到score的映射，而跳跃表里存放的是所有的成员，排序依据是HashMap里存的score。