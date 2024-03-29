# ThreadPool <!-- omit in toc -->

> 提供池化的线程使用方式，减少每次获取线程的消耗，提高对线程的利用率；

目录:

- [基础属性](#基础属性)
  - [什么是线程](#什么是线程)
  - [如何创建线程？](#如何创建线程)
- [知识点](#知识点)
  - [ThreadPoolExecutor 的构造参数](#threadpoolexecutor-的构造参数)
  - [Executor 的拒绝策略](#executor-的拒绝策略)
  - [线程池大小?](#线程池大小)
  - [Executors 的静态线程池存在哪些问题?](#executors-的静态线程池存在哪些问题)
  - [shutdown()和 shutdownNow()的区别](#shutdown和-shutdownnow的区别)
  - [isTerminated()和 isShutdown()的区别](#isterminated和-isshutdown的区别)
  - [Executor 的结构](#executor-的结构)
  - [使用线程池的好处](#使用线程池的好处)

## 基础属性

### 什么是线程

Thread 即 cpu 调度和分派的基本单位

### 如何创建线程？

- 实现 Runnable 接口，重写 run()方法
- 实现 Callable 接口，重写 call()方法
- 实例化 Thread 类
- 使用线程池获取

## 知识点

### ThreadPoolExecutor 的构造参数

- **corePoolSize**:最小可以同时运行的线程数量;
- **maximumPoolSize**:队列已满时,可以同时运行的最大线程数;
- **keepAliveTime**:线程数大于 corePoolSize 时,多余的空闲线程存活的最长时间
- **unit**:时间单位
- **workQueue**:线程数大于 corePoolSize 时,保存进队列;
- **threadFactory**:线程创建工厂
- **handler**:线程池的拒绝策略,线程数已达 maximumPoolSize 且队列已满时触发;

### Executor 的拒绝策略

当满足线程数已达 maximumPoolSize 且队列已满时触发决绝策略;

拒绝策略主要有以下几种:

- **AbortPolicy**:抛出`RejectedExecutionException`来拒绝新任务的处理;
- **CallerRunsPolicy**:将任务交给调用 Executor 的线程执行,若该线程已关闭则丢弃;
- **DiscardPolicy**:直接丢弃;
- **DiscardOldestPolicy**:丢弃最早的未处理任务;

### 线程池大小?

通常来说一般针对不同的任务类型设置不同的线程池大小(N 指 CPU 核心数):

- **CPU 密集型**: N+1
- **IO 密集型**: 2N

如何区分任务是哪种类型呢?

只要涉及到 IO 操作(**文件/网络等**)的均为 IO 密集型,其他为 CPU 密集型;

### Executors 的静态线程池存在哪些问题?

- **FixedThreadPool**和**SingleThreadExecutor**:允许请求的队列长度为 Integer.MAX_VALUE,可能堆积大量的请求，从而导致 OOM;
- **CachedThreadPool**和**ScheduledThreadPool**:允许创建的线程数量为 Integer.MAX_VALUE,可能会创建大量线程，从而导致 OOM;

### shutdown()和 shutdownNow()的区别

- **shutdown()**:关闭线程池，线程池的状态变为 SHUTDOWN。线程池不再接受新任务了，但是队列里的任务得执行完毕。
- **shutdownNow()**:关闭线程池，线程的状态变为 STOP。线程池会终止当前正在运行的任务，并停止处理排队的任务并返回正在等待执行的 List。

### isTerminated()和 isShutdown()的区别

- **isShutDown()**: 当调用 shutdown()方法后返回为 true。
- **isTerminated()**: 当调用 shutdown()方法后，并且所有提交的任务完成后返回为 true

### Executor 的结构

- **任务**：Runnable、Callable；
- **任务的执行**：ExecutorService；
- **异步计算结果**：Future

### 使用线程池的好处

- **降低资源消耗**：通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**：当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**：线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。
- 有效避免了 this 指针逃逸的问题；
- 线程工程及线程队列，拒绝策略等；
