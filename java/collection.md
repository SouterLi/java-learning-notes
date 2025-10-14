# 集合（Collection）
集合主要用于存储单列数据。  
Java集合框架提供了多种集合类型，包括：
1. **List**：有序的集合，允许重复元素。常用实现类有`ArrayList`和`LinkedList`。
2. **Set**：无序的集合，不允许重复元素。常用实现类有`HashSet`、`LinkedHashSet`和`TreeSet`。
3. **Queue**：用于存储待处理的元素，通常遵循先进先出（FIFO）的原则。常用实现类有`LinkedList`和`PriorityQueue`。
4. **Deque**：双端队列，允许在两端插入和删除元素。常用实现类有`ArrayDeque`和`LinkedList`。
5. **Stack**：后进先出（LIFO）的集合，常用实现类为`Stack`。

## ArrayList
### ArrayList的扩容机制
ArrayList的扩容机制是通过创建一个新的数组来实现的。当ArrayList的容量不足以容纳新的元素时，它会创建一个新的数组，通常是原数组大小的1.5倍或2倍，然后将原数组中的元素复制到新数组中。这个过程涉及以下几个步骤：
1. 检查当前数组的容量是否足够。
2. 如果容量不足，创建一个新的数组，大小为原数组的1.5倍或2倍。
3. 将原数组中的元素复制到新数组中。
4. 更新ArrayList的引用，使其指向新数组。
5. 释放原数组的内存。