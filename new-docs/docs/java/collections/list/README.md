# List <!-- omit in toc -->

> 一般用来保存顺序性的可重复的元素集合

目录:

- [主目录](#主目录)
- [基础属性](#基础属性)
- [知识点](#知识点)
  - [如何构建线程安全的 List](#如何构建线程安全的-list)

## 主目录

- [ArrayList](./arraylist.md)
- [LinkedList](./linkedlist.md)
- [CopyOnWriteArrayList](./copyonwritearraylist.md)

## 基础属性

Java 中的 List 主要包含以下几种类型

- LinkedList
- ArrayList
- CopyOnWriteArrayList

各自特性总结如下

| List 类型  |   默认属性   | 线程安全 | 数据结构 |             如何扩容              |
| :--------: | :----------: | :------: | :------: | :-------------------------------: |
| ArrayList  | 默认长度：10 |    否    |   数组   | 长度不足以容纳新元素时扩容 1.5 倍 |
| LinkedList |      -       |    否    | 双向链表 |                 -                 |

## 知识点

### 如何构建线程安全的 List

一般采用以下的方式保证 LIst 的线程安全

- Vector 数组
- Collections.synchronizedList()方法;
- 创建 CopyOnWriteArrayList 类型的 List

一般不采用 Vector 数组的方式,因为该 API 较为古老,应根据业务场景选择使用 Collections.synchronizedList()或 CopyOnWriteArrayList

- Collections.synchronizedList()通过在全部方法上添加 synchronized 关键字保证线程安全;
- CopyOnWriteArrayList 通过写时复制的方式保证线程安全[写入场景性能较差];
