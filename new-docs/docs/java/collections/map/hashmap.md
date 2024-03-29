# HashMap <!-- omit in toc -->

> HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。

目录:

- [基础属性](#基础属性)
- [知识点](#知识点)
  - [多线程使用 HashMap 导致的问题](#多线程使用-hashmap-导致的问题)
  - [如何在链表和红黑树间切换?](#如何在链表和红黑树间切换)
  - [如何扩容?](#如何扩容)

## 基础属性

- 线程不安全;
- 允许 key 和 value 为 null 值;
- 无序保存键值对;
- 默认数组长度 16
- 默认负载因子:0.75
- 数据结构:数组+链表[单向链表]/红黑树[双向链表]

## 知识点

### 多线程使用 HashMap 导致的问题

- 有可能导致链表结构中出现循环链表[该问题已修复];
- 特殊场景下的数据丢失问题;

### 如何在链表和红黑树间切换?

当满足以下条件时链表->红黑树:

- 链表长度>8;
- 数组长度大于 64;

当满足以下条件时红黑树->链表:

- 扩容时[resize],拆分的 high/low 两颗红黑树的节点数<6;
- 移除 key 时[remove],若红黑树满足退化条件[root 为空/root.right 为空/root.left 为空/root.left.left 为空]

### 如何扩容?

当满足以下条件时触发扩容机制

- 添加元素时
- 当前 Map 的 size()>数组长度\*负载因子

每次扩容时:

- 将数组长度翻倍[X2]
