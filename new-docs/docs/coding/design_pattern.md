# 设计模式 <!-- omit in toc -->

> 在软件工程中，设计模式（design pattern）是对软件设计中普遍存在（反复出现）的各种问题，所提出的解决方案;

目录:

- [知识点](#知识点)
  - [设计模式5大原则-SOLID](#设计模式5大原则-solid)
  - [创建型模式](#创建型模式)
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
    - [单例模式](#单例模式)
      - [单例模式失效场景](#单例模式失效场景)
      - [线程安全的懒汉模式实现](#线程安全的懒汉模式实现)
      - [饿汉模式实现](#饿汉模式实现)
      - [内部静态类实现](#内部静态类实现)
      - [枚举类型单例模式](#枚举类型单例模式)
  - [结构型模式](#结构型模式)
    - [适配器模式](#适配器模式)
      - [适配器模式实现](#适配器模式实现)
    - [桥接模式](#桥接模式)
      - [桥接模式实现](#桥接模式实现)
    - [组合模式](#组合模式)
      - [组合模式实现](#组合模式实现)
    - [装饰模式](#装饰模式)
      - [装饰模式实现](#装饰模式实现)
    - [外观模式](#外观模式)
      - [外观模式实现](#外观模式实现)
    - [享元模式](#享元模式)
      - [享元模式实现](#享元模式实现)
    - [代理模式](#代理模式)
      - [代理模式实现](#代理模式实现)
  - [行为模式](#行为模式)
    - [责任链模式](#责任链模式)
      - [责任链模式实现](#责任链模式实现)
    - [命令模式](#命令模式)
      - [命令模式实现](#命令模式实现)
    - [迭代器模式](#迭代器模式)
      - [迭代器模式实现](#迭代器模式实现)
    - [中介者模式](#中介者模式)
      - [中介者模式实现](#中介者模式实现)
    - [备忘录模式](#备忘录模式)
      - [备忘录模式实现](#备忘录模式实现)
    - [观察者模式](#观察者模式)
      - [观察者模式实现](#观察者模式实现)
    - [状态模式模式](#状态模式模式)
      - [状态模式实现](#状态模式实现)
    - [策略模式](#策略模式)
      - [策略模式实现](#策略模式实现)
    - [模板方法模式](#模板方法模式)
      - [模板方法模式实现](#模板方法模式实现)
    - [访问者模式](#访问者模式)
      - [访问者模式实现](#访问者模式实现)

## 知识点

### 设计模式5大原则-SOLID

-  S(单一职责原则):对象应该仅具有一种单一功能
-  O(开闭原则):软件应该是对于扩展开放的，但是对于修改封闭的
-  L(里氏替换原则):程序中的对象应该是可以在不改变程序正确性的前提下被它的子类所替换的
-  I(接口隔离原则):多个特定客户端接口要好于一个宽泛用途的接口
-  D(依赖倒置原则):依赖于抽象而不是一个实例

### 创建型模式

#### 简单工厂模式

> 简单工厂(静态工厂)属于创建型模式,是工厂模式的一种,大体来说简单工厂模式定义了一个创建对象的类,由这个类来封装实例化对象的行为,一般应用在大量创建某种/某类或某批对象时;

##### 简单工厂的实现

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

#### 工厂方法模式

> 定义一个用于创建对象的接口,让子类决定实例化哪个类

##### 工厂方法模式实现

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

#### 抽象工厂模式

> 定义多个抽象产品接口及对应的工厂接口,并在不同的工厂实现中获取对应的产品组合;

##### 抽象工厂模式实现

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

#### 创建者模式

> 使用创建者模式将构建对象的过程分离出来,方便快速按照不用的预设方案创建复杂对象

##### 创建者模式实现

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

#### 原型模式

> 原型模式是创建型模式的一种，其特点在于通过「复制」一个已经存在的实例来返回新的实例,而不是新建实例。

##### 原型模式实现

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

#### 单例模式

> 即一个类只有一个对象并且提供一个全局访问点;

主要由以下几种实现方式:

- **懒汉模式**:延迟创建对象,只有在第一次调取getInstance()时才生成对象,存在线程安全问题;
- **饿汉模式**:以静态变量的形式初始化单例对象,该对象将随着第一次该类被加载而一起初始化;
- **静态内部类**:单例对象在内部静态类中定义,本质也是一种懒加载,单例对象在第一次调取getInstance()时JVM加载内部静态类的同时完成单例对象创建;
- **枚举类型**:

##### 单例模式失效场景

- **反射攻击**:通过反射的方式修改构造函数访问修饰符并且完成调用生成对象;
- **反序列化攻击**:通过对象的序列化和反序列化机制实现对象创建;

| 攻击方式     | 懒汉模式                                                                | 饿汉模式                                                                | 静态内部类                                                              | 枚举 |
| :----------- | :---------------------------------------------------------------------- | :---------------------------------------------------------------------- | :---------------------------------------------------------------------- | :--- |
| 反射攻击     | 无解                                                                    | 不安全(可通过在私有构造函数种判断单例对象是否不为NULL解决)              | 不安全(可通过在私有构造函数种判断单例对象是否不为NULL解决)              | 安全 |
| 反序列化攻击 | 不安全(可通过实现Serializable接口并在readResolve方法中获取单例对象解决) | 不安全(可通过实现Serializable接口并在readResolve方法中获取单例对象解决) | 不安全(可通过实现Serializable接口并在readResolve方法中获取单例对象解决) | 安全 |

##### 线程安全的懒汉模式实现

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

##### 饿汉模式实现

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

##### 内部静态类实现

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

##### 枚举类型单例模式

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

### 结构型模式

#### 适配器模式

组合实现和继承实现有什么区别?

- 通过继承实现时将会对外暴露其父类的函数,违反迪米特法则;

##### 适配器模式实现

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

#### 桥接模式

> 桥接模式是一种结构型设计模式，可将一个大类或一系列紧密相关的类拆分为抽象和实现两个独立的层次结构，从而能在开发时分别使用。

##### 桥接模式实现

```java
/**
 * 抽象的绘图API接口
 */
interface DrawAPI {

    /**
     * 绘制圆形抽象函数
     *
     * @param radius 半径
     * @param x      起始坐标
     * @param y      起始坐标
     */
    void drawCircle(int radius, int x, int y);
}

/**
 * 红圈圈绘图实现类
 */
class RedCircle implements DrawAPI {

    /**
     * 绘制红圈圈
     *
     * @param radius 半径
     * @param x      起始坐标
     * @param y      起始坐标
     */
    @Override
    public void drawCircle(int radius, int x, int y) {
        System.out.println("red ...");
    }
}

/**
 * 抽象的形状类持有绘图接口对象
 */
abstract class Shape {

    /**
     * 内部的绘图API类对象
     */
    protected DrawAPI drawAPI;

    /**
     * 构造方法传入绘图API类
     *
     * @param drawAPI 绘图API对象
     */
    public Shape(DrawAPI drawAPI) {
        this.drawAPI = drawAPI;
    }

    /**
     * 形状类的抽象绘制方法
     */
    abstract void draw();
}

/**
 * 具体的圆圈实现类
 */
class Circle extends Shape {

    /**
     * 圆圈的基础属性
     */
    private int radius;
    private int x;
    private int y;

    /**
     * 构造方法传入圆圈的基础属性和绘图API对象
     *
     * @param radius  半径
     * @param x       起始坐标
     * @param y       起始坐标
     * @param drawAPI 绘图API
     */
    public Circle(int radius, int x, int y, DrawAPI drawAPI) {
        super(drawAPI);
        this.radius = radius;
        this.x = x;
        this.y = y;
    }

    @Override
    void draw() {
        super.drawAPI.drawCircle(radius, x, y);
    }
}
```

#### 组合模式

> 组合模式是一种结构型设计模式，你可以使用它将对象组合成树状结构，并且能像使用独立对象一样使用它们。

##### 组合模式实现

```java
```

#### 装饰模式

> 装饰模式是一种结构型设计模式， 允许你通过将对象放入包含行为的特殊封装对象中来为原对象绑定新的行为。

透明模板与半透明模式

- **透明模式**:要求客户端完全针对抽象编程，装饰模式的透明性要求客户端程序不应该将对象声明为具体构件类型或具体装饰类型，而应该全部声明为抽象构件类型。对客户端而言，具体构件类和具体装饰类对象没有任何区别;
- **半透明模式**:用具体装饰类型来定义装饰后的对象，而具体构件类型仍然可以使用抽象构件类型来定义，可以单独调用装饰的独有方法。

##### 装饰模式实现

```java
interface TextNotifiy {

    void show();
}

/**
 * 需要被装饰的类
 */
class MailTextNotifiy implements TextNotifiy {

    @Override
    public void show() {
        System.out.println("sending...mail");
    }
}

/**
 * 通过实现相同接口并引入被装饰对象的方式扩展方法功能
 */
class SMSTextNotifiyDecorator implements TextNotifiy {

    private TextNotifiy textNotifiy;

    public SMSTextNotifiyDecorator(TextNotifiy textNotifiy) {
        this.textNotifiy = textNotifiy;
    }

    /**
     * 在原有对象的基础上扩展新功能
     */
    @Override
    public void show() {
        textNotifiy.show();
        System.out.println("and sending...SMS");
    }
}

class QQTextNotifiyDecorator implements TextNotifiy {

    private TextNotifiy textNotifiy;

    public QQTextNotifiyDecorator(TextNotifiy textNotifiy) {
        this.textNotifiy = textNotifiy;
    }

    @Override
    public void show() {
        textNotifiy.show();
        System.out.println("and sending...QQ");
    }
}
```

#### 外观模式

> 外观模式是一种结构型设计模式，能为程序库、框架或其他复杂类提供一个简单的接口;

外观模式的意义

- 举一个例子,回家需要打开3个灯,1个热水器,1个电视,如果没有外观模式将在业务系统中直接依赖灯/热水器/电视等子系统对象,引入外观模式后将上述流程全部封装进一个goHome门面方法中,业务系统无需引入子系统的依赖仅依赖门面类即可;

##### 外观模式实现

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

#### 享元模式

> 享元模式是一种结构型设计模式，它摒弃了在每个对象中保存所有数据的方式，通过共享多个对象所共有的相同状态，让你能在有限的内存容量中载入更多对象。

享元模式的意义?

- 举一个实际的例子,比如要创建100个柳树对象,属性中仅有坐标不同,这样创建出来的话重复的属性比如树名称/种类等被重复的创建因此产生了浪费,若将这部分属性集中到一个对象中,其他树对象持有这个对象的引用即可大大降低内存空间的占用;

##### 享元模式实现

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

#### 代理模式

> 代理模式是一种结构型设计模式，让你能够提供对象的替代品或其占位符。代理控制着对于原对象的访问，并允许在将请求提交给对象前后进行一些处理。

##### 代理模式实现

```java
/**
 * 实现代理模式的共有接口
 */
interface Game {

    /**
     * 被代理对象的业务方法
     */
    void play();
}

/**
 * 真被代理的对象
 */
class GenshinImpact implements Game {

    /**
     * 真实对象的业务实现方法
     */
    @Override
    public void play() {
        System.out.println("this is Genshin Impact playing...");
    }
}

/**
 * 真实对象的代理类
 */
class GenshinImpactProxy implements Game {

    /**
     * 持有的真实对象
     */
    private GenshinImpact genshinImpact;

    public GenshinImpactProxy(GenshinImpact genshinImpact) {
        this.genshinImpact = genshinImpact;
    }

    /**
     * 代理对象调用持有的真实对象的业务方法
     */
    @Override
    public void play() {
        genshinImpact.play();
        this.afterPlay();
    }

    /**
     * 代理对象在原有真实对象的基础上扩展的方法
     */
    private void afterPlay() {
        System.out.println("this is after Genshin Impact playing...");
    }
}
```

### 行为模式

#### 责任链模式

> 责任链模式是一种行为设计模式，允许你将请求沿着处理者链进行发送。收到请求后，每个处理者均可对请求进行处理，或将其传递给链上的下个处理者

##### 责任链模式实现

```java
/**
 * 责任链的基础结构
 */
abstract class Handler {

    /**
     * 责任链中用于指向下一个节点的指针
     */
    private Handler next;

    public void setNext(Handler next) {
        this.next = next;
    }

    /**
     * 抽象的责任链处理函数,交由具体子类实现
     *
     * @param parms 用于业务处理的参数
     * @return 业务处理结果, 判断是否需要向后接着处理
     */
    abstract protected boolean check(Object parms);

    /**
     * 公共的用于判断是否接着处理下一个责任链节点的函数
     *
     * @param parms 判断依赖的参数
     * @return 是否继续处理的判断结果
     */
    protected boolean checkNext(Object parms) {
        if (next == null) {
            return true;
        }
        return check(parms);
    }
}

/**
 * 具体子类的责任链实现
 */
class LoginHandler extends Handler {

    /**
     * 子类结合业务逻辑具体实现的校验郭泽
     *
     * @param parms 用于业务处理的参数
     * @return 该流程的校验结果
     */
    @Override
    protected boolean check(Object parms) {
        if (parms == null) {
            return false;
        }
        return checkNext(parms);
    }
}

/**
 * 另一种子类的实现方式
 */
class ThrottlingHandler extends Handler {

    /**
     * 检查子类的处理事件是否符合要求,不满足则延时
     *
     * @param parms 用于业务处理的参数
     * @return 该流程的校验结果
     */
    @Override
    protected boolean check(Object parms) {
        if (parms != null && System.nanoTime() <= 100000) {
            // todo 阻塞当前线程延迟一定时间
            // Thread.currentThread().wait(1000);
        }
        return checkNext(parms);
    }
}
```

#### 命令模式

> 命令模式是一种行为设计模式，它可将请求转换为一个包含与请求相关的所有信息的独立对象。该转换让你能根据不同的请求将方法参数化、延迟请求执行或将其放入队列中，且能实现可撤销操作。

##### 命令模式实现

```java
```

#### 迭代器模式

> 迭代器模式是一种行为设计模式，让你能在不暴露集合底层表现形式（列表、 栈和树等）的情况下遍历集合中所有的元素。

##### 迭代器模式实现

```java
```

#### 中介者模式

> 中介者模式是一种行为设计模式，能让你减少对象之间混乱无序的依赖关系。该模式会限制对象之间的直接交互，迫使它们通过一个中介者对象进行合作。

##### 中介者模式实现

```java
```

#### 备忘录模式

> 备忘录模式是一种行为设计模式，允许在不暴露对象实现细节的情况下保存和恢复对象之前的状态。

##### 备忘录模式实现

```java
```

#### 观察者模式

> 观察者模式是一种行为设计模式，允许你定义一种订阅机制，可在对象事件发生时通知多个“观察”该对象的其他对象。

##### 观察者模式实现

```java
/**
 * 通用的观察对象接口
 */
interface Observerable {

    /**
     * 被观察的对象发生事件时会调用该方法
     *
     * @param message 事件消息
     */
    void update(String message);
}

/**
 * 被观察的对象
 */
class ObserverSubject {

    /**
     * 用于容纳观察者的容器
     */
    private List<Observerable> observerables = new ArrayList<>();

    /**
     * 添加观察者的方法
     *
     * @param observerable 观察者对象
     */
    public void addObserver(Observerable observerable) {
        observerables.add(observerable);
    }

    /**
     * 移除观察者的方法
     *
     * @param observerable 观察者对象
     */
    public void deleteObserver(Observerable observerable) {
        observerables.remove(observerable);
    }

    /**
     * 对象发生变更时发送消息的方法
     *
     * @param message 通知的消息
     */
    public void sendMessage(String message) {
        for (Observerable observerable : observerables) {
            observerable.update(message);
        }
    }
}
```

#### 状态模式模式

> 状态模式是一种行为设计模式，让你能在一个对象的内部状态变化时改变其行为，使其看上去就像改变了自身所属的类一样。

##### 状态模式实现

```java
/**
 * 定义抽象的状态接口
 */
interface State {

    /**
     * 该状态执行函数
     */
    void handler();
}

/**
 * 一个具体的状态实现函数
 */
class HappyState implements State {

    @Override
    public void handler() {
        System.out.print("this is happy ...");
    }
}

/**
 * 一个具体的状态实现函数
 */
class SadState implements State {

    @Override
    public void handler() {
        System.out.print("this is sad ...");
    }
}

/**
 * 一个抽象的转台使用者这里用Person标识
 */
class Person {

    private State state;

    /**
     * 必备的状态更新函数
     *
     * @param state 状态对象
     */
    public void setState(State state) {
        this.state = state;
    }

    /**
     * 真实的业务函数
     */
    public void doAction() {
        this.state.handler();
    }
}
```

#### 策略模式

> 策略模式是一种行为设计模式， 它能让你定义一系列算法， 并将每种算法分别放入独立的类中， 以使算法的对象能够相互替换。

##### 策略模式实现

```java
/**
 * 抽象的策略接口用于抽象算法
 */
interface PaymentStrategy {

    /**
     * 抽象出来的支付方法
     *
     * @param amount 支付金额
     * @return 是否支付成功
     */
    boolean pay(int amount);
}

/**
 * PayPal实现策略
 */
class PayPalPayment implements PaymentStrategy {

    @Override
    public boolean pay(int amount) {
        System.out.println("PayPalPayment...");
        return true;
    }
}

/**
 * PayPal实现策略
 */
class ZhiFuBaoPayment implements PaymentStrategy {

    @Override
    public boolean pay(int amount) {
        System.out.println("ZhiFuBaoPayment...");
        return true;
    }
}

/**
 * 使用到策略的具体实现类
 */
class Order {

    /**
     * 实现类中的策略抽象
     */
    private PaymentStrategy payment;

    public Order() {
    }

    public Order(PaymentStrategy payment) {
        this.payment = payment;
    }

    public PaymentStrategy getPayment() {
        return payment;
    }

    public void setPayment(PaymentStrategy payment) {
        this.payment = payment;
    }

    /**
     * 在实现中调取抽象的算法方法
     *
     * @param amount 支付金额
     * @return 支付结果
     */
    public boolean pay(int amount) {
        return payment.pay(amount);
    }
}
```

#### 模板方法模式

> 模板方法模式是一种行为设计模式，它在超类中定义了一个算法的框架， 允许子类在不修改结构的情况下重写算法的特定步骤。

##### 模板方法模式实现

```java
/**
 * 父层抽象类用于定义函数的骨架
 */
public abstract class BaseCalss {

    /**
     * 主要函数的骨架方法,其中step1/step2均为固定步骤直接提供实现,step3的实现将由子类提供
     */
    public void start() {
        step1();
        step2();
        step3();
    }

    private void step1() {
        System.out.println("this is step 1 ...");
    }

    private void step2() {
        System.out.println("this is step 2 ...");
    }

    /**
     * 子类实现的step3,可以表现多态
     */
    protected abstract void step3();
}
public class SubClass extends BaseCalss {

    /**
     * 在子类中具体实现的步骤三
     */
    @Override
    protected void step3() {
        System.out.println("this is My Step 3 ...");
    }
}
```

#### 访问者模式

> 访问者模式是一种行为设计模式，它能将算法与其所作用的对象隔离开来。

##### 访问者模式实现

```java
/**
 * 抽象的访问者接口,其中包含全部类型的访问者函数以适应不同的实例业务对象
 */
interface Visitor {

    void doRedAction();

    void doGreenAction();
}

/**
 * 具体的访问者实现类,实现了在每种情况下的访问者部分业务逻辑
 */
class BasicVisitor implements Visitor {
    @Override
    public void doRedAction() {
        System.out.print("this is Red ...");
    }

    @Override
    public void doGreenAction() {
        System.out.print("this is Green ...");
    }
}

/**
 * 抽象的业务实体对象,其中包含了接收访问者接口的抽象函数
 */
abstract class Shape {

    private int x;
    private int y;

    /**
     * 用于接收访问者对象的抽象函数
     *
     * @param visitor 访问者对象
     */
    abstract void accept(Visitor visitor);
}

/**
 * 具体的业务对象类
 */
class RedShape extends Shape {

    /**
     * 接收传入的访问者对象并调用跟自己业务相关的方法
     *
     * @param visitor 访问者对象
     */
    @Override
    void accept(Visitor visitor) {
        visitor.doRedAction();
    }
}

/**
 * 具体的业务对象类
 */
class GreenShape extends Shape {

    /**
     * 接收传入的访问者对象并调用跟自己业务相关的方法
     *
     * @param visitor 访问者对象
     */
    @Override
    void accept(Visitor visitor) {
        visitor.doGreenAction();
    }
}
```