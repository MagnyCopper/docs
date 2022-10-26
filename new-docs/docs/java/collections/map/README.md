# Map<!-- omit in toc -->

> 一般用来保存映射关系,即 key-value 对形式的数据.其中 key 是唯一值,value 可以重复.

目录:

- [主目录](#主目录)
- [基础属性](#基础属性)
- [知识点](#知识点)
  - [负载因子的作用](#负载因子的作用)
  - [rehash 的的含义](#rehash-的的含义)
  - [为什么线程安全的 Map 优先选择 ConcurrentHashMap](#为什么线程安全的-map-优先选择-concurrenthashmap)

## 主目录

- [HashMap](./hashmap.md)
- [ConcurrentHashMap](./concurrent_hashmap.md)
- [HashTable](./hashtable.md)
- [LinkedHashMap](./linkedhashmap.md)
- [TreeMap](./treemap.md)

## 基础属性

Java 中的 Map 主要包含以下几种类型

- HashMap
- LinkedHashMap
- HashTable
- TreeMap
- ConcurrentHashMap

各自特性总结如下:

|     Map 类型      |                 默认属性                  | 线程安全 | key 为空 | value 为空 | 顺序性 |                      扩容机制                       |
| :---------------: | :---------------------------------------: | :------: | :------: | :--------: | :----: | :-------------------------------------------------: |
|      HashMap      |       数组长度:16<br>负载因子:0.75        |    否    |    是    |     是     |  无序  |  Map 的 size()=数组长度\*负载因子时数组长度变为 2N  |
|   LinkedHashMap   |        组长度:16<br>负载因子:0.75         |    否    |    是    |     是     |  有序  |  Map 的 size()=数组长度\*负载因子时数组长度变为 2N  |
|     HashTable     |       数组长度:11<br>负载因子:0.75        |    是    |    否    |     否     |  无序  | Map 的 size()=数组长度\*负载因子时数组长度变为 2N+1 |
|      TreeMap      |                     -                     |    否    |    否    |     是     |  有序  |                          -                          |
| ConcurrentHashMap | 数组长度:16<br>负载因子:0.75<br>并发度:16 |    是    |    否    |     否     |  无序  |  Map 的 size()=数组长度\*负载因子时数组长度变为 2N  |

## 知识点

### 负载因子的作用

负载因子越大[可以大于 1]:

- 数组的利用率越高;
- 链表长度变大;
- 数组的空间利用率较高[容易发生 hash 碰撞]

负载因子越小:

- 链表长度变小[查询更快];
- 数组空间利用率较差;

### rehash 的的含义

是指当内部数组长度不足或其他原因导致触发数组扩容时,重新对原有的全部键值对重新计算 hash 值并在新数组安放原有全部元素的过程;

### 为什么线程安全的 Map 优先选择 ConcurrentHashMap

Map 的线程安全主要有以下 3 种方式：

- HashTable
- Collections.synchronizedMap(new HashMap())
- ConcurrentHashMap

上述 3 种方式中：HashTable 和 Collections.synchronizedMap(new HashMap())均是通过在全部 get()/put()等方法上添加 synchronized 关键字来保证线程安全的,而在 ConcurrentHashMap 中大量使用 CAS 无锁编程的方式实现线程安全，因此性能更好；
