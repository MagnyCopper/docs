# CompletableFuture <!-- omit in toc -->

> CompletableFuture是一个非常有用的异步编程类,源自JDK8;

目录:

- [知识点](#知识点)
  - [如何创建CompletableFuture?](#如何创建completablefuture)
  - [run方法和supply方法的区别](#run方法和supply方法的区别)
  - [CompletionStage方法总结](#completionstage方法总结)

## 知识点

### 如何创建CompletableFuture?

主要通过以下3种方式:

- `new`关键字;
- CompletableFuture的静态方法`completedFuture()`,`failedFuture()`等;
- CompletableFuture的静态方法`runAsync()`,`supplyAsync()`等;

### run方法和supply方法的区别

- **runAsync()**:无返回值
- **supplyAsync()**:有返回值;

### CompletionStage方法总结

xxx()与xxxAsync()决定是否开启单独线程执行函数

|  方法名称  |          说明           |
| :--------: | :---------------------: |
|   xxx()    |      在原线程执行       |
| xxxAsync() | 由默认/指定的线程池执行 |

