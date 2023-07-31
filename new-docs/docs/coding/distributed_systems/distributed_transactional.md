# 分布式事务 <!-- omit in toc -->

> 用于微服务场景或分布式架构下的应用程序,帮助其保证原子性;

目录:

- [简单介绍](#简单介绍)
- [2PC](#2pc)
- [3PC](#3pc)
- [基础属性](#基础属性)
- [其他知识点](#其他知识点)
  - [第一个知识点 XXXXXX](#第一个知识点-xxxxxx)

## 简单介绍

分布式事务的主流方案

- 2PC
- 3PC
- TCC
- AT
- Seata

## 2PC

即2段式提交,事务流程分为2个阶段和2个主要角色

主要角色:

1. **事务管理器**:即协调者;
2. **资源管理器**:一般就是各个业务系统自己的数据库;

主要流程:

1. **prepare**:事务管理器向所有本地资源管理器发起请求，询问是否是 ready 状态，所有参与者都将本事务能否成功的信息反馈发给协调者;
2. **commit/rollback**:事务管理器根据所有本地资源管理器的反馈，通知所有本地资源管理器，步调一致地在所有分支上提交或者回滚;

存在的问题

1. **同步阻塞**:当参与事务者存在占用公共资源的情况，其中一个占用了资源，其他事务参与者就只能阻塞等待资源释放，处于阻塞状态。;
2. **单点故障**:一旦事务管理器出现故障，整个系统不可用;
3. **数据不一致**:在阶段二，如果事务管理器只发送了部分 commit 消息，此时网络发生异常，那么只有部分参与者接收到 commit 消息，也就是说只有部分参与者提交了事务，使得系统数据不一致;
4. **不确定性**:当协事务管理器发送 commit 之后，并且此时只有一个参与者收到了 commit，那么当该参与者与事务管理器同时宕机之后，重新选举的事务管理器无法确定该条消息是否提交成功;

## 3PC

本质是一种2PC的优化方案,解决了部分2PC的问题

改进点:

1. 添加了一个准备提交阶段(canConmit,preCommit,doCommit/doRollback),减少了资源的持有时间;
2. 在事务管理器和资源管理器端均引入过期时间,解决了部分由于网络延迟导致的数据不一致;

主要流程:

1. **canConmit**:向全部资源管理发送canConmit命令,得到全部YES答复后,进入preCommit阶段,反之取消事务;
2. **preCommit**:向所有资源管理者发送资源锁定命令,资源管理者成功锁定后均返回YES,则进入doCommit流程,若存在部分资源管理者返回NO或者事务管理器长时间未收到回复则进入doRollback
3. **doCommit/doRollback**:向全部资源管理器发送doCommit/doRollback命令(资源管理者在指定时间内未收到提交命令也将自动提交),资源管理者完成事务提交后向事务管理器返回ACK,若部分资源管理器未返回或返回超时,事务管理器将发送中止事务指令;

## 基础属性

- 属性 1;
- 属性 2;

## 其他知识点

### 第一个知识点 XXXXXX

知识点的描述部分