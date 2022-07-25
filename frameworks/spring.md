# Spring

> Spring框架是 Java 平台的一个开源的全栈（full-stack）应用程序框架和控制反转容器实现，一般被直接称为 Spring。该框架的一些核心功能理论上可用于任何 Java 应用，但 Spring 还为基于Java企业版平台构建的 Web 应用提供了大量的拓展支持。

## 概念解释

- **IOC**:Inverse of Control-控制反转
- **AOP**:Aspect-Oriented Programming-面向切面编程
- **bean**:IoC容器所管理的对象;

## 知识点

### 什么是 Spring IoC?

Inverse of Control-控制反转 是一种设计思想,即**将原本在程序中手动创建对象的控制权，交由框架来管理**

- 控制:指的是对象创建（实例化、管理）的权力
- 反转:控制权交给外部环境（Spring 框架、IoC 容器）

对象间的依赖关系及生命周期均交由框架来管理,用户在使用时仅需告诉框架需要什么对象即可;

对应到Spring框架上来说:

IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map,存放的是各种对象;

IoC 最常见以及最合理的实现方式叫做依赖注入（Dependency Injection，简称 DI）

### 什么是Spring Aop

Spring AOP 是基于动态代理实现的分为两种情况:需要代理的对象有无接口实现?

- 有接口:使用JDK Proxy创建代理对象
- 无接口:使用Cglib创建被代理对象的子类

### Spring AOP与AspectJ AOP的区别

- Spring AOP 属于运行时增强,功能较为简单;
- AspectJ 是编译时增强,功能复杂性能较好;

### Bean的作用域

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的，对单例设计模式的应用。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- session : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。

### 单例Bean线程安全吗?

单例bean并不是线程安全的,应尽量少使用局部变量,必要时应使用ThreadLocal进行变量保存;

### Bean的生命周期

1. Bean 容器找到配置文件中 Spring Bean 的定义;
2. Bean 容器利用 Java Reflection API 创建一个 Bean 的实例;
3. 如果实现了 Aware接口，就调用相应的set方法;
4. 如果有和这个Bean的Spring容器相关的BeanPostProcessor对象,则执行其postProcessBeforeInitialization()方法;
5. 如果实现了InitializingBean接口,则调用afterPropertiesSet()方法;
6. 调用bean定义时init-method指明的方法;
7. 如果有和这个Bean的Spring容器相关的BeanPostProcessor对象,则执行其postProcessAfterInitialization()方法;
8. bean交由程序使用;
9. 如果实现了DisposableBean接口,则调用destroy()方法;
10. 调用bean定义时destroy-method指明的方法;

### Spring中使用到了哪些设计模式?

- 工厂设计模式 : Spring 使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。
- 代理设计模式 : Spring AOP 功能的实现。
- 单例设计模式 : Spring 中的 Bean 默认都是单例的。
- 模板方法模式 : Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- 包装器设计模式 : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据- 需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数- 据源。
- 观察者模式: Spring 事件驱动模型就是观察者模式很经典的一个应用。
- 适配器模式 : Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller。

### Spring 事务

Spring支持以下2种方式管理事务:

- 编程式事务:注入TransactionTemplate对象
- 声明式事务:添加@Transactional注解

#### Spring 事务的传播行为(7个)

- PROPAGATION_REQUIRED:默认值,如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务;
- PROPAGATION_REQUIRES_NEW:创建一个新的事务，如果当前存在事务，则把当前事务挂起;
- PROPAGATION_NESTED:如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行,若没有事务等价于PROPAGATION_REQUIRED
- PROPAGATION_MANDATORY:如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- PROPAGATION_SUPPORTS:如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- PROPAGATION_NOT_SUPPORTED:以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- PROPAGATION_NEVER:以非事务方式运行，如果当前存在事务，则抛出异常。

#### Spring事务的隔离级别

- ISOLATION_DEFAULT:使用后端数据库的默认的隔离级别;
- ISOLATION_READ_UNCOMMITTED :可以读取未提交的事务,脏读、幻读或不可重复读均会发生;
- ISOLATION_READ_COMMITTED :可以读取已提交事务的数据,阻止脏读，但是幻读或不可重复读仍有可能发生;
- ISOLATION_REPEATABLE_READ :同一字段多次读取结果一致,阻止脏读和不可重复读，但幻读仍有可能发生;
- ISOLATION_SERIALIZABLE :串行化,全部事务依次执行,防止脏读、不可重复读以及幻读;

#### Spring事务的rollBackFor属性

默认情况仅回滚RuntimeException,若加上rollbackFor=Exception.class即可在非运行时异常也出发回滚;