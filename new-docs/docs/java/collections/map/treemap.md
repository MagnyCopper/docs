# TreeMap <!-- omit in toc -->

> 这也是一个能排序的 Map;

目录:

- [基础属性](#基础属性)
- [知识点](#知识点)
  - [TreeMap 的顺序性](#treemap-的顺序性)

## 基础属性

- 线程不安全;
- 允许 key 不能为 null，value 可以;
- 有序保存键值对;
- 数据结构:红黑树

## 知识点

### TreeMap 的顺序性

TreeMap 能够保证的遍历 Map 的顺序性即按 key 的字典排序[默认]；

构造方法的 Comparable 参数可以用于自定义排序方法:
