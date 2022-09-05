# ThreadLocal <!-- omit in toc -->

> ThreadLocal 是 Thread 的局部变量,多个线程的 ThreadLocal 互不影响

目录:

- [基础属性](#基础属性)
- [知识点](#知识点)
  - [InheritableThreadLocal 说明](#inheritablethreadlocal-说明)
  - [ThreadLocalMap 说明](#threadlocalmap-说明)
  - [ThreadLocalMap 扩容说明](#threadlocalmap-扩容说明)
  - [ThreadLocal 的内存泄露](#threadlocal-的内存泄露)

## 基础属性

主要包含以下方法:

| 方法名        | 说明                       |
| :------------ | :------------------------- |
| get()         | 获取当前线程中保存的值     |
| remove()      | 移除当前线程中保存的值     |
| set()         | 初始化并在当前线程中保存值 |
| withInitial() | 指定初始化值               |

主要属性如下:

- 底层数据结构为 Map(ThreadLocalMap);
- 一般使用静态变量方式创建`public static final ThreadLocal<?> threadLocal = new ThreadLocal<?>();`

## 知识点

### InheritableThreadLocal 说明

InheritableThreadLocal 主要用来解决父子线程间进行值传递的需求；子线程创建时将自动同步父线程的 InheritableThreadLocal 值

### ThreadLocalMap 说明

- ThreadLocalMap 底层为 Entry 数组结构,初始长度：16;
- 使用黄金分割数作为 hash 的计算因子,大大减少了 Hash 冲突的概率;
- key 弱引用，value 强引用；
- 在调用`get()`,`set()`,`remove()`方法时会进行探测式和启发式数据清理，进行内存回收；
- 发生 hash 冲突时将顺序向后查找位置写入,查找过程中发现 key 为 null 的数据时将触发探测式数据清理;

### ThreadLocalMap 扩容说明

1. 老 size + 1 = 新 size
2. 如果新 size 大于等于老 size 的 2/3 时，需要考虑扩容。
3. 扩容前先尝试回收一次 key 为 null 的值，腾出一些空间。（rehash）
4. 如果回收之后发现 size 还是大于等于老 size 的 1/2 时，才需要真正的扩容。
5. 每次都是按 2 倍的大小扩容。

### ThreadLocal 的内存泄露

在线程池的线程中使用 ThreadLocal，由于线程池的线程并不会释放导致 ThreadLocal 产生内存泄露，需要注意；

> 如果用线程池来操作 ThreadLocal 对象确实会造成内存泄露, 因为对于线程池里面不会销毁的线程, 里面总会存在着<ThreadLocal, LocalVariable>的强引用, 因为 final static 修饰的 ThreadLocal 并不会释放, 而 ThreadLocalMap 对于 Key 虽然是弱引用, 但是强引用不会释放, 弱引用当然也会一直有值, 同时创建的 LocalVariable 对象也不会释放, 就造成了内存泄露; 如果 LocalVariable 对象不是一个大对象的话, 其实泄露的并不严重, 泄露的内存 = 核心线程数 \* LocalVariable 对象的大小;
