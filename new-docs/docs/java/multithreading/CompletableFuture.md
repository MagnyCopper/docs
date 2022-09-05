# CompletableFuture <!-- omit in toc -->

> CompletableFuture 是一个非常有用的异步编程类,源自 JDK8;

目录:

- [知识点](#知识点)
  - [如何创建 CompletableFuture?](#如何创建-completablefuture)
  - [run 方法和 supply 方法的区别](#run-方法和-supply-方法的区别)
  - [CompletableFuture 方法总结](#completablefuture-方法总结)
  - [CompletionStage 方法总结](#completionstage-方法总结)

## 知识点

### 如何创建 CompletableFuture?

主要通过以下 3 种方式:

- `new`关键字;
- CompletableFuture 的静态方法`completedFuture()`,`failedFuture()`等;
- CompletableFuture 的静态方法`runAsync()`,`supplyAsync()`等;

### run 方法和 supply 方法的区别

- **runAsync()**:无返回值
- **supplyAsync()**:有返回值;

### CompletableFuture 方法总结

全部方法说明如下:

| 方法名称 | 说明                 |
| :------- | :------------------- |
| allOf()  | 全部子任务完成后执行 |
| anyOf()  | 任一子任务完成后执行 |
| get()    | 阻塞获取任务返回值   |
| join()   | 阻塞获取任务返回值   |

### CompletionStage 方法总结

全部方法说明如下:

| 方法名称              | 说明                                  |
| :-------------------- | :------------------------------------ |
| acceptEither()        | 上游 2 个任一完成时,无返回值,当前线程 |
| acceptEitherAsync()   | 上游 2 个任一完成时,无返回值,新线程   |
| applyToEither()       | 上游 2 个任一完成时,有返回值,当前线程 |
| applyToEitherAsync()  | 上游 2 个任一完成时,有返回值,新线程   |
| exceptionally()       | 上游异常时,有返回值,当前线程          |
| handle()              | 上游正常/异常时,有返回值,当前线程     |
| handleAsync()         | 上游正常/异常时,有返回值,当前线程     |
| runAfterBoth()        | 上游 2 个都完成时,无返回值,当前线程   |
| runAfterBothAsync()   | 上游 2 个都完成时,无返回值,新线程     |
| runAfterEither()      | 上游 2 个任一完成时,无返回值,当前线程 |
| runAfterEitherAsync() | 上游 2 个任一完成时,无返回值,新线程   |
| thenAccept()          | 上游完成时,无返回值,当前线程          |
| thenAcceptAsync()     | 上游完成时,无返回值,新线程            |
| thenAcceptBoth()      | 上游 2 个都完成时,无返回值,当前线程   |
| thenAcceptBothAsync() | 上游 2 个都完成时,无返回值,新线程     |
| thenApply()           | 上游完成时,有返回值,当前线程          |
| thenApplyAsync()      | 上游完成时,有返回值,新线程            |
| thenCombine()         | 上游 2 个都完成时,有返回值,当前线程   |
| thenCombineAsync()    | 上游 2 个都完成时,有返回值,新线程     |
| thenCompose()         | 上游完成时,无返回值,当前线程          |
| thenComposeAsync()    | 上游完成时,无返回值,新线程            |
| thenRun()             | 上游完成时,无返回值,当前线程          |
| thenRunAsync()        | 上游完成时,无返回值,新线程            |
| whenComplete()        | 上游正常/异常时,无返回值,当前线程     |
| whenCompleteAsync()   | 上游正常/异常时,无返回值,新线程       |
