# AQS <!-- omit in toc -->

> AbstractQueuedSynchronizer(AQS)抽象队列同步器,ReentrantLock/Semaphore/ReentrantReadWriteLock均是基于此进行开发的;

**目录**:

- [知识点](#知识点)
  - [核心思想](#核心思想)
  - [主要方法](#主要方法)
  - [工作流程](#工作流程)
    - [排他锁](#排他锁)
    - [共享锁](#共享锁)

## 知识点

### 核心思想

AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，如果被请求的共享资源被占用，即将暂时获取不到锁的线程加入到队列中

### 主要方法

需要复写的方法含义如下:

- **isHeldExclusively()**：该线程是否正在独占资源。只有用到condition才需要去实现它。
- **tryAcquire(int)**：独占方式。尝试获取资源，成功则返回true，失败则返回false。
- **tryRelease(int)**：独占方式。尝试释放资源，成功则返回true，失败则返回false。
- **tryAcquireShared(int)**：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
- **tryReleaseShared(int)**：共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。

### 工作流程

#### 排他锁

加锁流程:从`acquire()`方法开始

1. 调用`tryAccquire()`尝试获取锁;
2. 若锁获取失败则执行`addWaiter()`以CAS方式创建一个Node节点并设置为队列尾节点;
3. 执行`acquireQueued()`方法休眠线程,并自旋等待唤醒;

解锁流程:从`release()`方法开始

1. 调用`tryRelease()`尝试释放锁;
2. 若释放锁成功则执行`unparkSuccessor()`方法唤醒队列中首个节点;
3. 若首个节点状态异常则尝试从尾节点往前找状态正常的Node唤醒;

#### 共享锁

加锁流程:从`acquireShared()`方法开始

1. 调用`tryAcquireShared()`尝试获取锁;
2. 若锁获取失败则执行`doAcquireShared()`首先以CAS方式创建Node并设置为队列尾节点;
3. 若前驱节点为head则再次调用`tryAcquireShared()`方法若成功则更新head并唤醒后继线程;
4. 若前驱节点不是Head执行`shouldParkAfterFailedAcquire()`方法休眠线程,并自旋等待唤醒;

解锁流程:从`releaseShared()`方法开始

1. 调用`tryReleaseShared()`尝试释放锁;
2. 若释放锁成功则执行`doReleaseShared()`方法唤醒队列中首个节点并向后传播唤醒其他节点;