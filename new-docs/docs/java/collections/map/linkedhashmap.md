# LinkedHashMap <!-- omit in toc -->

> 这是一个有顺序的 Map;

目录:

- [基础属性](#基础属性)
- [知识点](#知识点)
  - [LinkedHashMap 的顺序性](#linkedhashmap-的顺序性)
  - [LinkedHashMap 如何保证顺序性](#linkedhashmap-如何保证顺序性)
  - [简易 LRU 实现](#简易-lru-实现)
  - [如何在链表和红黑树间切换](#如何在链表和红黑树间切换)
  - [如何扩容](#如何扩容)

## 基础属性

- 线程不安全;
- 允许 key 和 value 为 null 值;
- 有序保存键值对;
- 默认数组长度 16
- 默认负载因子:0.75
- 数据结构:数组+链表/红黑树[双向链表]

## 知识点

### LinkedHashMap 的顺序性

LinkedHashMap 能够保证的遍历 Map 的顺序性即按 put 的顺序进行遍历[默认]；

构造方法的 accessOrder 参数决定了排序的依据:

- True：按照最近未被 put/get 的顺序遍历[操作后的 key 移至最后];
- False：按照 put 的顺序遍历;

### LinkedHashMap 如何保证顺序性

LinkedHashMap 底层通过每个链表节点的 after 和 before 属性来保证顺序性，after 和 before 可以跨链表结构进行关联；

保证顺序性主要依靠一下 4 个方法:

- afterNodeAccess(Node p)方法实现了在 put/get 节点操作后将节点移至末尾;
- afterNodeInsertion(boolean evict)方法实现了在执行插入节点后维护链表,如实现一个固定长度的 LRU 缓存;
- afterNodeRemoval(Node p)方法实现了在移除节点后维护链表顺序的功能;
- linkNodeLast()方法实现了创建一个新节点并维护链表顺序;

### 简易 LRU 实现

利用 LinkedHashMap 的 removeEldestEntry()可以实现一个简单的固定长度 LRU 缓存

```java
Map<Long, Object> cacheLRU = Collections.synchronizedMap(new LinkedHashMap<Long, Object>(100, .75f, true) {
@Override
protected boolean removeEldestEntry(Map.Entry<Long, Object> eldest) {
return size() > 100;
}
});
```

### 如何在链表和红黑树间切换

当满足以下条件时链表->红黑树:

- 链表长度>8;
- 数组长度大于 64;

当满足以下条件时红黑树->链表:

- 扩容时[resize],拆分的 high/low 两颗红黑树的节点数<6;
- 移除 key 时[remove],若红黑树满足退化条件[root 为空/root.right 为空/root.left 为空/root.left.left 为空]

### 如何扩容

当满足以下条件时触发扩容机制

- 添加元素时
- 当前 Map 的 size()>数组长度\*负载因子

每次扩容时:

- 将数组长度翻倍[X2]
