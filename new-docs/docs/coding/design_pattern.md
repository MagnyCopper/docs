# 设计模式 <!-- omit in toc -->

> 在软件工程中，设计模式（design pattern）是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案;

目录:

- [知识点](#知识点)
  - [单例模式](#单例模式)
    - [单例模式失效场景](#单例模式失效场景)
    - [线程安全的懒汉模式实现](#线程安全的懒汉模式实现)
    - [饿汉模式实现](#饿汉模式实现)
    - [内部静态类实现](#内部静态类实现)
    - [枚举类型单例模式](#枚举类型单例模式)
  - [简单工厂模式](#简单工厂模式)
    - [简单工厂的实现](#简单工厂的实现)
  - [工厂方法模式](#工厂方法模式)
    - [工厂方法模式实现](#工厂方法模式实现)
  - [抽象工厂模式](#抽象工厂模式)
    - [抽象工厂模式实现](#抽象工厂模式实现)
  - [创建者模式](#创建者模式)
    - [创建者模式实现](#创建者模式实现)

## 知识点

### 单例模式

> 即一个类只有一个对象并且提供一个全局访问点;

主要由以下几种实现方式:

- **懒汉模式**:延迟创建对象,只有在第一次调取getInstance()时才生成对象,存在线程安全问题;
- **饿汉模式**:以静态变量的形式初始化单例对象,该对象将随着第一次该类被加载而一起初始化;
- **静态内部类**:单例对象在内部静态类中定义,本质也是一种懒加载,单例对象在第一次调取getInstance()时JVM加载内部静态类的同时完成单例对象创建;
- **枚举类型**:

#### 单例模式失效场景

- **反射攻击**:通过反射的方式修改构造函数访问修饰符并且完成调用生成对象;
- **反序列化攻击**:通过对象的序列化和反序列化机制实现对象创建;

| 攻击方式     | 懒汉模式                                                                | 饿汉模式                                                                | 静态内部类                                                              | 枚举 |
| :----------- | :---------------------------------------------------------------------- | :---------------------------------------------------------------------- | :---------------------------------------------------------------------- | :--- |
| 反射攻击     | 无解                                                                    | 不安全(可通过在私有构造函数种判断单例对象是否不为NULL解决)              | 不安全(可通过在私有构造函数种判断单例对象是否不为NULL解决)              | 安全 |
| 反序列化攻击 | 不安全(可通过实现Serializable接口并在readResolve方法中获取单例对象解决) | 不安全(可通过实现Serializable接口并在readResolve方法中获取单例对象解决) | 不安全(可通过实现Serializable接口并在readResolve方法中获取单例对象解决) | 安全 |

#### 线程安全的懒汉模式实现

```java
class LazySingleton implements Serializable {

    /**
     * 序列化版本号
     */
    private static final long serialVersionUID = 42L;

    /**
     * 单例对象
     * volatile关键字实现了禁用指令重排序和保证可见性,防止在并发场景下出现拿到未初始化完成的对象;
     */
    private volatile static LazySingletonDemo instance;

    /**
     * 私有构造函数,防止从构造函数创建对象
     */
    private LazySingleton() {
    }

    /**
     * 获取单例对象函数
     * 使用双重检查和加锁的方式保证线程安全
     *
     * @return 该类的唯一实例
     */
    public static LazySingletonDemo getInstance() {
        // 双重检查的第一次检查
        if (instance == null) {
            // 加锁锁住类对象
            synchronized (LazySingletonDemo.class) {
                // 已加锁后的第二次检查
                if (instance == null) {
                    instance = new LazySingletonDemo();
                }
            }
        }
        return instance;
    }

    /**
     * 通过添加该方法阻止反序列化时由JVM自动生成对象
     *
     * @return 单例对象
     * @throws ObjectStreamException 对象反序列化发生异常
     */
    private Object readResolve() throws ObjectStreamException {
        return getInstance();
    }
}
```

#### 饿汉模式实现

```java
class HungrySingleton implements Serializable {

    /**
     * 序列化版本号
     */
    private static final long serialVersionUID = 42L;

    /**
     * 在类定义时直接采用静态变量的方式初始化,该方式由JVM的类加载器保证线程安全
     */
    private static final HungrySingleton instance = new HungrySingleton();

    /**
     * 私有构造函数,防止从构造函数创建对象
     */
    private HungrySingleton() {
        // 通过以下代码防止通过反射调用构造方法破坏单例模式
        if (getInstance() != null) {
            throw new RuntimeException("单例模式被破坏");
        }
    }

    /**
     * 获取单例对象函数
     *
     * @return 该类的唯一实例
     */
    public static HungrySingleton getInstance() {
        return instance;
    }

    /**
     * 通过添加该方法阻止反序列化时由JVM自动生成对象
     *
     * @return 单例对象
     * @throws ObjectStreamException 对象反序列化发生异常
     */
    private Object readResolve() throws ObjectStreamException {
        return getInstance();
    }
}
```

#### 内部静态类实现

```java
class InnerClassSingleton implements Serializable {

    /**
     * 序列化版本号
     */
    private static final long serialVersionUID = 42L;

    /**
     * 用于持有单例对象的静态内部类
     * 当调用getInstance()方法时随着单例对象持有类的初始化一并完成单例对象的初始化,且由JVM的类加载器保证线程安全
     */
    private static class InnerClassSingletonHolder {

        /**
         * 单例对象
         */
        private static final InnerClassSingleton instance = new InnerClassSingleton();
    }

    /**
     * 私有构造函数,防止从构造函数创建对象
     */
    private InnerClassSingleton() {
        // 通过以下代码防止通过反射调用构造方法破坏单例模式
        if (getInstance() != null) {
            throw new RuntimeException("单例模式被破坏");
        }
    }

    /**
     * 获取单例对象函数
     *
     * @return 该类的唯一实例
     */
    public static InnerClassSingleton getInstance() {
        return InnerClassSingletonHolder.instance;
    }

    /**
     * 通过添加该方法阻止反序列化时由JVM自动生成对象
     *
     * @return 单例对象
     * @throws ObjectStreamException 对象反序列化发生异常
     */
    private Object readResolve() throws ObjectStreamException {
        return getInstance();
    }
}
```

#### 枚举类型单例模式

```java
/**
 * 创建枚举类型对象
 */
public enum EnumSingleton {

    /**
     * 单例对象
     */
    INSTANCE;

    /**
     * 私有构造函数,防止外部调用
     */
    EnumSingleton() {
    }
}
```

### 简单工厂模式

> 简单工厂(静态工厂)属于创建型模式,是工厂模式的一种,大体来说简单工厂模式定义了一个创建对象的类,由这个类来封装实例化对象的行为,一般应用在大量创建某种/某类或某批对象时;

#### 简单工厂的实现

```java
/**
 * 定义产品行为接口
 */
interface Product {

    /**
     * 抽象共性产品方法声明
     */
    void action();
}

/**
 * 产品A的类定义
 * 实现了抽象产品接口
 */
class ProductA implements Product {

    @Override
    public void action() {
        System.out.println("ProductA action ...");
    }
}

/**
 * 产品B的类定义
 * 实现了抽象产品接口
 */
class ProductB implements Product {

    @Override
    public void action() {
        System.out.println("ProductB action ...");
    }
}

/**
 * 简单工程类
 */
class SimpleFactory {

    /**
     * 通过静态方法根据传入的参数实例化对应的产品对象
     *
     * @param productType 产品类型
     * @return 产品对象
     */
    public static Product getProdct(String productType) {
        if (productType.equals("A")) {
            return new ProductA();
        } else if (productType.equals("B")) {
            return new ProductB();
        } else {
            return null;
        }
    }
}
```

### 工厂方法模式

> 定义一个用于创建对象的接口,让子类决定实例化哪个类

#### 工厂方法模式实现

```java
/**
 * 定义产品行为接口
 */
interface Product {

    /**
     * 抽象共性产品方法声明
     */
    void action();
}

/**
 * 产品A的类定义
 * 实现了抽象产品接口
 */
class ProductA implements Product {

    @Override
    public void action() {
        System.out.println("ProductA action ...");
    }
}

/**
 * 产品B的类定义
 * 实现了抽象产品接口
 */
class ProductB implements Product {

    @Override
    public void action() {
        System.out.println("ProductB action ...");
    }
}

/**
 * 定义抽象的工厂接口
 */
interface FactoryMethodInterface {

    /**
     * 该方法用于描绘该类型的全部工厂的公有方法
     *
     * @return 返回对应的产品
     */
    Product getProduct();
}

/**
 * A产品的生产工厂
 */
class ProductAFactory implements FactoryMethodInterface {

    @Override
    public Product getProduct() {
        return new ProductA();
    }
}

/**
 * B产品的生产工厂
 */
class ProductBFactory implements FactoryMethodInterface {

    @Override
    public Product getProduct() {
        return new ProductB();
    }
}
```

### 抽象工厂模式

> 定义多个抽象产品接口及对应的工厂接口,并在不同的工厂实现中获取对应的产品组合;

#### 抽象工厂模式实现

```java
/**
 * 定义其他产品抽象
 */
interface Car {

    void run();
}

/**
 * car类型产品A
 */
class SportCar implements Car {

    @Override
    public void run() {
        System.out.println("Sport Car ...");
    }
}

/**
 * car类型产品B
 */
class FlyCar implements Car {

    @Override
    public void run() {
        System.out.println("Fly Car ...");
    }
}

/**
 * 定义产品行为接口
 */
interface Product {

    /**
     * 抽象共性产品方法声明
     */
    void action();
}

/**
 * 产品A的类定义
 * 实现了抽象产品接口
 */
class ProductA implements Product {

    @Override
    public void action() {
        System.out.println("ProductA action ...");
    }
}

/**
 * 产品B的类定义
 * 实现了抽象产品接口
 */
class ProductB implements Product {

    @Override
    public void action() {
        System.out.println("ProductB action ...");
    }
}

/**
 * 定义抽象的工厂接口
 */
interface FactoryMethodInterface {

    /**
     * 该方法用于描绘该类型的全部工厂的公有方法
     *
     * @return 返回对应的产品
     */
    Product getProduct();

    /**
     * 第二种产品的对应方法
     *
     * @return 反会对应的产品
     */
    Car getCar();
}

/**
 * A产品的生产工厂
 */
class ProductAFactory implements FactoryMethodInterface {

    @Override
    public Product getProduct() {
        return new ProductA();
    }

    @Override
    public Car getCar() {
        return new SportCar();
    }
}

/**
 * B产品的生产工厂
 */
class ProductBFactory implements FactoryMethodInterface {

    @Override
    public Product getProduct() {
        return new ProductB();
    }

    @Override
    public Car getCar() {
        return new FlyCar();
    }
}
```

### 创建者模式

> 使用创建者模式将构建对象的过程分离出来,方便快速按照不用的预设方案创建复杂对象

#### 创建者模式实现

```java
/**
 * 基础产品创建接口主要包含实际产品属性的各个set方法以及返回最终产品的方法
 */
interface CarBuilder {

    void buildSpeed(int speed);

    void buildPrice(int price);

    void buildName(String name);

    void buildType();

    Car build();
}

/**
 * 运动型汽车的创建类实现
 */
class SportCarBuilder implements CarBuilder {

    private Car car = new Car();

    @Override
    public void buildSpeed(int speed) {
        car.setSpeed(speed + 1000);
    }

    @Override
    public void buildPrice(int price) {
        car.setPrice(price);
    }

    @Override
    public void buildName(String name) {
        car.setName(name);
    }

    @Override
    public void buildType() {
        car.setType("SportCar");
    }

    @Override
    public Car build() {
        return car;
    }
}

/**
 * 运动型汽车的创建类实现
 */
class FlyCarBuilder implements CarBuilder {

    private Car car = new Car();

    @Override
    public void buildSpeed(int speed) {
        car.setSpeed(speed);
    }

    @Override
    public void buildPrice(int price) {
        car.setPrice(price + 1000);
    }

    @Override
    public void buildName(String name) {
        car.setName(name);
    }

    @Override
    public void buildType() {
        car.setType("FlyCar");
    }

    @Override
    public Car build() {
        return car;
    }
}


class CarBuildDirector {

    private CarBuilder carBuilder;

    public CarBuildDirector(CarBuilder carBuilder) {
        this.carBuilder = carBuilder;
    }

    public Car build(int speed, int price, String name) {
        carBuilder.buildSpeed(speed);
        carBuilder.buildPrice(price);
        carBuilder.buildName(name);
        return carBuilder.build();
    }
}

/**
 * 具体的产品类
 */
class Car {

    private int speed;
    private int price;
    private String name;

    private String type;

    public void setSpeed(int speed) {
        this.speed = speed;
    }

    public void setPrice(int price) {
        this.price = price;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setType(String type) {
        this.type = type;
    }
}
```
