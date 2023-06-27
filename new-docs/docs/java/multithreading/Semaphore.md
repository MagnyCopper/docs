# Semaphore <!-- omit in toc -->

> Semaphore也叫信号量，在JDK1.5被引入，可以用来控制同时访问特定资源的线程数量，通过协调各个线程，以保证合理的使用资源。

目录:

- [简单介绍](#简单介绍)
- [基础属性](#基础属性)
- [其他知识点](#其他知识点)

## 简单介绍

信号量就类似一个停车场,在初始化创建时需要指定停车场得车位数量(许可数量),排队规则(是否公平即来先先进),总体来说就是保证多线程场景下最多存在N个线程同时访问资源得一个工具;

当车辆入场时调用`acquire()`方法申请入场,如果有车位那就直接进入,否则等待;

车辆离场时调用`release()`方法,标识车位空出;

具体代码样例如下:

```java
    private static final Semaphore SEMAPHORE = new Semaphore(3);

    @Override
    public void run() throws Exception {
        for (int i = 0; i <= 10; i++) {
            Thread thread = new Thread(() -> {
                try {
                    StopWatch stopWatch = new StopWatch();
                    stopWatch.start();
                    SEMAPHORE.acquire();
                    log.info("等待时间:{}", stopWatch.getTotalTimeMillis());
                    Thread.sleep(5 * 1000);
                    SEMAPHORE.release();
                } catch (Exception e) {
                    log.error("", e);
                }
            });
            thread.start();
        }
    }
```

## 基础属性

- 信号量底层是基于AQS框架实现的;

## 其他知识点


