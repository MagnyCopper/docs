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
  - [原型模式](#原型模式)
    - [原型模式实现](#原型模式实现)
  - [享元模式](#享元模式)
    - [享元模式实现](#享元模式实现)
  - [外观模式](#外观模式)
    - [外观模式实现](#外观模式实现)
  - [适配器模式](#适配器模式)
    - [适配器模式实现](#适配器模式实现)

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

### 原型模式

> 原型模式是创建型模式的一种，其特点在于通过「复制」一个已经存在的实例来返回新的实例,而不是新建实例。

#### 原型模式实现

```java
class Person implements Cloneable {

    private String name;

    public Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    protected Person clone() throws CloneNotSupportedException {
        return (Person) super.clone();
    }
}

/**
 * 用于测试原型模式的类
 * 只有实现了Cloneable才能够调用Object.clone()方法
 */
class Car implements Cloneable {

    private int price;
    private Person person;

    public Car(int price, Person person) {
        this.price = price;
        this.person = person;
    }

    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }

    public Person getPerson() {
        return person;
    }

    public void setPerson(Person person) {
        this.person = person;
    }

    /**
     * 用于执行复制对象的方法
     *
     * @return 该对象this的复制对象
     * @throws CloneNotSupportedException 对象未添加Cloneable标识时抛出
     */
    @Override
    protected Car clone() throws CloneNotSupportedException {
        // 调用Object.clone()仅能实现基础数据类型及其包装类类型的属性复制,一旦涉及引用对象将复制引用
        Car car = (Car) super.clone();
        // 需要通过递归的调用的对应属性本身的clone方法实现对象的深拷贝
        Person person = this.getPerson().clone();
        car.setPerson(person);
        // 如果对象结构十分复杂,可以考虑采用对象的序列化和反序列机制实现对象的深复制;
        return car;
    }
}
```

### 享元模式

> 享元模式是一种结构型设计模式，它摒弃了在每个对象中保存所有数据的方式，通过共享多个对象所共有的相同状态，让你能在有限的内存容量中载入更多对象。

享元模式的意义?

- 举一个实际的例子,比如要创建100个柳树对象,属性中仅有坐标不同,这样创建出来的话重复的属性比如树名称/种类等被重复的创建因此产生了浪费,若将这部分属性集中到一个对象中,其他树对象持有这个对象的引用即可大大降低内存空间的占用;

#### 享元模式实现

```java
/**
 * 享元对象
 * 一个对象中相对静态的属性集中在这个类中
 */
class Tree {

    /**
     * 由于name属性相对时静态因此使用final修饰由构造函数进行初始化
     */
    private final String name;

    private final int type;

    public Tree(String name, int type) {
        this.name = name;
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public int getType() {
        return type;
    }
}

/**
 * 业务对象
 * 类结构中直接引用享元对象类
 * 这个栗子中是将相同的树种在不同的位置因此tree是作为享元出现的
 * 而坐标作为变化数据为每个对象独有的
 */
class TreeNode {

    private int x;

    private int y;

    private Tree tree;

    public TreeNode(int x, int y, Tree tree) {
        this.x = x;
        this.y = y;
        this.tree = tree;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public Tree getTree() {
        return tree;
    }
}

/**
 * 一些情况下也可以使用工厂的方式提供享元对象
 */
class TreeFactory {

    /**
     * 享元对象的缓存
     */
    private static final Map<String, Tree> TREE_CACHE = new ConcurrentHashMap<>();

    /**
     * 用来提供享元对象的静态方法懒加载
     *
     * @param name 树对象的名称
     * @param type 树对象的类型
     * @return 树对象
     */
    public static Tree getTree(String name, int type) {
        if (TREE_CACHE.containsKey(name)) {
            return TREE_CACHE.get(name);
        }
        Tree tree = new Tree(name, type);
        TREE_CACHE.put(name, tree);
        return tree;
    }
}
```

### 外观模式

> 外观模式是一种结构型设计模式，能为程序库、框架或其他复杂类提供一个简单的接口;

外观模式的意义

- 举一个例子,回家需要打开3个灯,1个热水器,1个电视,如果没有外观模式将在业务系统中直接依赖灯/热水器/电视等子系统对象,引入外观模式后将上述流程全部封装进一个goHome门面方法中,业务系统无需引入子系统的依赖仅依赖门面类即可;

#### 外观模式实现

```java
class Light {

    public void open() {
        System.out.println("opening...light");
    }
}

class Heater {

    public void open() {
        System.out.println("opening...heater");
    }
}

class TV {

    public void open() {
        System.out.println("opening...tv");
    }
}

/**
 * 门面类依赖各个子系统并封装子系统的业务方法对外暴露服务
 */
class Facade {

    private Light light1 = new Light();
    private Light light2 = new Light();
    private Light light3 = new Light();

    private Heater heater = new Heater();
    private TV tv = new TV();

    public void goHome() {
        light1.open();
        light2.open();
        light3.open();
        heater.open();
        tv.open();
    }
}
```

### 适配器模式

组合实现和继承实现有什么区别?

- 通过继承实现时将会对外暴露其父类的函数,违反迪米特法则;

#### 适配器模式实现

被适配对象及需要提供的接口

```java
/**
 * 需要被适配的类,电源输出220v电压
 */
class Power {

    /**
     * 电源的输出方法
     *
     * @return 220v电压
     */
    public int output() {
        return 220;
    }
}

/**
 * 使用方期待使用的函数
 */
interface TargetPower {

    /**
     * 期待获取5v电压输出
     *
     * @return 5v电压输出
     */
    int output5v();
}
```

组合实现:

```java
class PowerAdapter implements TargetPower {

    /**
     * 内部引用被适配的对象
     */
    private Power power;

    public PowerAdapter(Power power) {
        this.power = power;
    }

    /**
     * 在接口实现中完成转换方法
     *
     * @return 输出5v电压
     */
    @Override
    public int output5v() {
        int output = power.output();
        // 一系列花里胡哨的处理
        output = 5;
        return output;
    }
}
```

继承实现:

```java
class PowerAdapter extends Power implements TargetPower {

    /**
     * 继承后直接引用父类方法并完成转换
     *
     * @return 5v电压
     */
    @Override
    public int output5v() {
        int output = output();
        // 一系列花里胡哨的处理
        output = 5;
        return output;
    }
}
```