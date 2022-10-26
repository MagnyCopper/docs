# CopyOnWriteArrayList <!-- omit in toc -->

> 这是一个线程安全的 List

目录:

- [基础属性](#基础属性)
- [知识点](#知识点)
  - [CopyOnWriteArrayList 如何保证线程安全](#copyonwritearraylist-如何保证线程安全)
  - [CopyOnWriteArrayList 遍历时能否添加元素](#copyonwritearraylist-遍历时能否添加元素)

## 基础属性

- 线程安全
- 有序保存元素
- 默认数组长度 0
- 数据结构:数组
- 支持快速随机访问

## 知识点

### CopyOnWriteArrayList 如何保证线程安全

在内部使用 ReentrantLock 来保证线程安全

同时在底层 Object[]前使用 volatile 修饰来保证可见性,仅能保证最终一致性

### CopyOnWriteArrayList 遍历时能否添加元素

CopyOnWriteArrayList 数组在遍历时是基于访问时瞬间的快照实现遍历的;

因此在遍历过程中的修改是无法被察觉的
