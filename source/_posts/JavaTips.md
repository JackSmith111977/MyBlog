---
title: Java笔记(持续更新...)
date: 2025-09-03 21:45:00
tags: Java
---


# Java知识点
---
## 目录

> [静态变量、静态方法](#静态变量静态方法)
> [继承](#继承)
> [多态](#多态)
> [final关键字](#final-关键字)
> [设计模式](#设计模式)
> [枚举类](#枚举类)
> [抽象类](#抽象类)
> [接口](#接口)
> [代码块](#代码块)
> [内部类](#内部类)
> [函数式编程](#函数式编程)
> [常用API](#常用api)


---
## 静态变量、静态方法

[返回目录](#目录)

### 定义
1. **静态变量（类变量）**：属于类，只加载一份，可以被类和类的全部对象访问
2. **实例变量（对象变量）**：属于对象，每个对象有自己一份
3. **静态方法（类方法）**：属于类，只能访问静态变量，不能访问实例变量
4. **实例方法（对象方法）**：属于对象

### 应用
1. **静态变量**的应用实例: 记录对象的创建数量
2. **静态方法**应用实例：对于只需要完成某个功能，而不需要直接访问对象的数据的方法，可以使用静态方法
~~~java
public class Student {
    // 静态变量（类变量）：属于类，只加载一份，可以被类和类的全部对象访问
    static String schoolName;
    // 实例变量（对象变量）：属于对象，每个对象有自己一份
    String name;
    // 静态变量的应用实例: 记录对象的创建数量
    static Integer count = 0 ;


    public Student(String name){
        this.name = name;
        // 同一个类中，访问静态变量，类名可以不写
        count++;
    }

    public Student(){
        count++;
    }

    // 静态方法（类方法）：属于类，只能访问静态变量，不能访问实例变量
    // 静态方法应用实例：对于只需要完成某个功能，而不需要直接访问对象的数据的方法，可以使用静态方法
    public static void printHelloWorld(){
        System.out.println("Hello World");
    }

    // 实例方法（对象方法）：属于对象
    public void printName(){
        System.out.println(name);
    }

}
~~~

### 注意事项
1. **静态方法**可以直接访问**静态变量**，不能直接访问**实例变量**
2. **实例方法**既可以直接访问**静态变量**，也可以直接访问**实例变量**
3. **静态方法**不能使用`this`关键字（不属于对象，自然不可以用`this`）

### 访问方式
1. **静态变量**访问方式：**类名**.静态成员变量名<font color=red>（推荐方式）</font>
2. **静态变量**访问方式：**对象名**.静态成员变量名（不推荐）
3. **静态方法**访问方式：**类名**.静态方法名

## 继承

[返回目录](#目录)

### 子类构造器的特点
1. 子类的全部构造器，都会先调用父类的构造器，再调用自己的构造器
2. 默认情况下，子类构造器的第一行都是`super();`(**写不写都有**)调用父类构造器，可自行指定有参还是无参

### 兄弟构造器
1. 构造器中可以使用`this(参数);`调用兄弟构造器，可用于为某个参数赋予默认值
2. `super();`和`this();`必须写在构造器的**第一行**，并且**不能同时使用**

## 多态

[返回目录](#目录)

### 认识多态
1. 多态是在**继承/实现**情况下的一种现象，表现为**对象多态、行为多态**
```java
public class main {
    public static void main(String[] args) {
        // 对象多态
        Animal wolf = new Wolf();
        Animal dog = new Dog();

        // 行为多态
        wolf.run();
        System.out.println(wolf.name);
        dog.run();
        System.out.println(dog.name);

        // 多态的好处2：定义方法时，使用父类的形参，可以接收一切子类对象，拓展性强
        goRun(wolf);
        goRun(dog);

        // 强制类型转换，解决多态下无法调用子类独有方法的问题
        // 注意：对象的真实类型与强制转换的类型不同，会导致ClassCastException异常
        Wolf newWolf = (Wolf)wolf;
        Dog newDog = (Dog)dog;
        newWolf.howl();
        newDog.bark();

        // instanceof 判断对象是否是某个类的实例
        if(wolf instanceof Dog){
            Dog newNewDog = (Dog)wolf;
        } else if(wolf instanceof Wolf){
            Wolf newNewWolf = (Wolf)wolf;
        }

    }

    // 多态的好处2：定义方法时，使用父类的形参，可以接收一切子类对象，拓展性强
    public static void goRun(Animal animal){
        animal.run();
    }
}

public class Animal {
    String name = "animal";
    public void run(){
        System.out.println("running");
    }
}

public class Dog extends Animal{
    String name = "Dog";
    @Override
    public void run()
    {
        System.out.println("Dog is running");
    }

    // 多态无法调用子类独有的方法
    public void bark()
    {
        System.out.println("Dog is barking");
    }
}

public class Wolf extends Animal{
    String name = "Wolf";
    @Override
    public void run()
    {
        System.out.println("Wolf is running");
    }

    // 多态无法调用子类独有的方法
    public void howl()
    {
        System.out.println("Wolf is howling");
    }
}
```
2. `run()`方法：编译看左`Animal`，运行看右`Dog/Wolf`
3. `name`属性：编译看左`Animal`，运行看也左`Animal`

### 多态的特性
1. 多态模式下，右边的对象是解耦合的，便于拓展和维护
2. 定义方法时，使用父类的形参，可以接收一切子类对象，拓展性强
3. 多态模式下，无法调用子类独有的方法
4. 多态模式下，强制类型转换成子类对象，此时可以调用子类独有的方法
5. 对象的真实类型与强制转换的类型不同，会导致`ClassCastException`异常
6. `instanceof` 判断对象是否是某个类的实例，强制类型转换之前可以检查是否是某个类的实例

## final 关键字

[返回目录](#目录)

### 基本概念
1. `final` 关键字修饰类：表示该类不能被继承，称为**最终类**
2. `final` 关键字修饰方法：表示该方法不能被重写，称为**最终方法**
3. `final` 关键字修饰变量：表示该变量有且仅能赋值一次

### 使用场景
1. 使用`final`修饰**静态变量**，可以声明一个**常量**，通常作为系统配置信息，不再修改(常量通常全大写，多个单词用下划线连接，如`MAX_SIZE`)
2. 使用`final`修饰**基本变量**，变量储存的**数据**不能改变
3. 使用`final`修饰**引用变量**(类对象、数组对象)，变量存储的**地址**不能改变，但对象内部数据可以改变

## 设计模式

[返回目录](#目录)

### 单例设计模式
#### 作用
确保某个类只创建一个对象

#### 实现方式
##### 饿汉式单例--用对象之前已创建完毕
1. 将类的构造器私有
2. 定义一个类变量记住一个对象
3. 定义一个类方法获取对象

~~~java
// 饿汉式单例
public class A {
    // 1. 私有化构造器：确保单例类对外不能创建对象
    private A(){
    }

    // 2. 创建静态成员变量记住唯一的一个实例
    private static final A a = new A();

    // 3. 创建静态方法，返回唯一实例
    public static A getInstance(){
        return a;
    }
}
~~~

##### 懒汉式单例--用对象时才创建对象

~~~java
// 懒汉式单例
public class B {
    // 私有化构造器
    private B(){}

    // 静态变量记住唯一实例
    private static B b;

    // 静态方法获取唯一实例
    public static B getInstance(){
        if(b == null){
            b = new B();
        }
        return b;
    }
}
~~~

---
### 模板方法设计模式
#### 基本概念
提供一个方法作为完成某类功能的模板，模板方法封装了每个实现步骤，但允许子类提供特定步骤的实现
#### 实现方式
1. 定义模板方法，把共同实现步骤放进去
2. 定义抽象方法，交给具体的子类完成
3. 建议使用`final`修饰模板方法，防止被重写

~~~java
public abstract class People {
    // 模板方法设计模式
    public final void write(){
        System.out.println("重复部分代码");
        // 模板方法知道子类一定要重写的部分
        // 使用抽象方法来实现重写部分
        writeMain();

        System.out.println("重复部分代码");
    }

    // 定义一个抽象方法，子类必须实现
    public abstract void writeMain();
}

public class Student extends People{

    @Override
    public void writeMain() {
        System.out.println("学生写代码");
    }
}

public class Teacher extends People{
    @Override
    public void writeMain() {
        System.out.println("老师写代码");
    }
}
~~~

## 枚举类
[返回目录](#目录)

### 特点
1. 枚举类都是最终类，不能被继承，都继承自`java.lang.Enum`
2. 枚举类第一行只能罗列一些名称，这些名称是常量，每个常量会记住枚举类的一个对象
3. 枚举类的构造器都是私有的，不能对外创建对象
~~~java
// 反编译后的枚举类结构
public final class com.kei.enumDemo.A extends java.lang.Enum<com.kei.enumDemo.A> {
  public static final com.kei.enumDemo.A X;
  public static final com.kei.enumDemo.A Y;
  public static final com.kei.enumDemo.A Z;
  public static com.kei.enumDemo.A[] values();
  public static com.kei.enumDemo.A valueOf(java.lang.String);
  static {};
}
~~~

### Api
1. `name()`：返回枚举常量的名称
2. `ordinal()`：返回枚举常量的索引，索引从0开始

### 使用场景
1. 枚举类一般用于信息分类和标志
2. 枚举类作为形参可以约束传入参数


## 抽象类
[返回目录](#目录)
### 抽象类和抽象方法
1. 抽象类：抽象类不能实例化对象，只能被继承
2. 抽象方法：抽象方法没有方法体，方法体由子类实现

## 接口
[返回目录](#目录)
### 基本概念
`interface` 关键字定义接口
1. 定义常量，接口中的常量public static final 可以不写，默认是public static final
2. 定义抽象方法，接口中的抽象方法public abstract 可以不写，默认是public abstract
3. 接口不能实例化对象
4. 接口可以被实现类通过`implements`关键字**实现**，一个类只能**继承**一个父类，但可以**实现**多个接口
5. 实现类必须实现所有接口中的抽象方法，否则必须声明抽象类

### 接口的好处
1. 解决了单继承的不足，使类的角色更多
2. 面相接口编程，进一步解耦合

### JDK8 新特性
1. 默认方法：使用`default`关键字定义，默认被`public`修饰，只能由接口实现类对象调用，实际上是对象（实例）方法
2. 私有方法：使用`private`关键字定义，只能由接口内部其他实例调用，不能被继承
3. 静态方法：使用`static`关键字定义，默认被`public`修饰，只能由接口名称调用，实际上是类方法

### 注意事项
1. 多继承：**接口**可以继承多个**接口**
2. 多实现：**接口**可以被多个**类**实现
3. 一个接口继承多个接口时，若存在签名冲突，则不支持多继承和多实现
4. 一个接口继承多个接口时，若存在同名默认方法，重写该方法可以不冲突
5. 一个类继承了父类，同时实现了接口，若有同名方法，则父类方法优先
6. 一个类继承了父类，同时实现了接口，若有同名方法，可通过`接口名.super.接口方法()`调用接口方法


## 代码块

[返回目录](#目录)

### 静态代码块
**格式**：`static{ }`
**特点**：**类**加载时自动执行，只执行一次
**作用**：完成**类**的初始化，静态变量的初始化赋值

### 实例代码块
**格式**：`{ }`
**特点**：**对象**创建时自动执行，每次创建对象都会执行
**作用**：完成**对象**的初始化，实例变量的初始化赋值

## 内部类

[返回目录](#目录)

### 基本概念
当一个类定义在另一个类的内部，这个类就是内部类
当一个类的内部，包含一个完整的事物，且这个事物没必要单独设计，这个类可以被设计为内部类

### 成员内部类
1. 定义在外部类内，无`static`修饰，属于**外部类对象持有**的
2. 创建内部类对象的语法：`外部类名.内部类名 对象名 = new 外部类对象().new 内部类名()`
3. 成员内部类可以直接访问外部类**静态成员**，也可以访问外部类**实例成员**（成员内部类寄生于外部类对象）
4. 成员内部类可以通过`外部类名.this`访问外部类对象

### 静态内部类
1. 定义在外部类内，有`static`修饰，属于**外部类持有**的
2. 创建内部类对象的语法：`外部类名.内部类名 对象名 = new 外部类名.内部类名()`
3. 静态内部类可以直接访问外部类**静态成员**，不能访问外部类**实例成员**

### 匿名内部类
1. 匿名内部类是一种特殊的局部内部类，不需要声明类名，默认有一个隐藏的名称
2. 匿名内部类本质是一个子类，并且会立即创建一个子类对象，编译后会生成一个 `类名$编号.class` 文件
~~~java
new 类或接口(参数){
    类体（一般是方法重写）
}
~~~
3. 用于更方便创建子类

## 函数式编程

[返回目录](#目录)

### Lambda 表达式
1. 语法：`(参数列表) -> {方法体}`
2. 函数式接口：只有一个抽象方法的接口
3. 可以简化函数式接口的匿名内部类

### 静态方法引用
1. 格式：`类名::静态方法名`
2. 如果一个Lambda表达式里只调用一个静态方法，并且`->`前后的参数的形式一致，则可以使用静态方法引用

### 实例方法引用
1. 语法：`对象::实例方法名`
2. 如果一个Lambda表达式里只调用一个实例方法，并且`->`前后的参数的形式一致，则可以使用实例方法引用

### 特定类型方法引用
1. 语法：`特定类名::实例方法名`
2. 如果某个Lambda表达式里只是调用一个特定类型的实例方法，并且前面参数列表的第一个参数作为方法的主调，后面所有参数都是作为实例方法的入参，则可以使用特定类型方法引用

~~~java
public class Main2 {
    public static void main(String[] args) {
        // 需求：一个英文人名数组，按字母升序排序
        String[] names = {"Tom", "Jerry", "Mike", "Mary", "Smith", "Kate", "tom", "jam", "milk"};

        // 排序
        Arrays.sort(names);
        // 忽略首字母大小写
        Arrays.sort(names, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                // compareToIgnoreCase(): 忽略大小写
                return o1.compareToIgnoreCase(o2);
            }
        });

        // Lambda 表达式
        Arrays.sort(names, (o1, o2) -> o1.compareToIgnoreCase(o2));

        // 特定的方法引用：类名::方法名
        Arrays.sort(names, String::compareToIgnoreCase);

        System.out.println(Arrays.toString(names));

    }
}
~~~

### 构造器引用
1. 语法：`类名::new`
2. 如果某个Lambda表达式里只是在创建对象，并且`->`前后的参数形式一致，则可以使用构造器引用

~~~java
public class Main3 {
    public static void main(String[] args) {
        CarFactory factory = new CarFactory() {
            @Override
            public Car create(String name) {
                return new Car(name);
            }
        };
        // Lambda 表达式
        CarFactory factory2 = (name) -> new Car(name);

        // 构造器引用
        CarFactory factory3 = Car::new;
    }

}

@FunctionalInterface
interface CarFactory {
    Car create(String name);
}

@Data
@NoArgsConstructor
@AllArgsConstructor
class Car{
    private String name;
}
~~~

## 常用API

[返回目录](#目录)

> [String](#string)
> [ArrayList](#arraylist)

### String
#### 创建字符串
1. **方式一**：`String str = "hello world";`
2. 方式二：调用构造器创建字符串对象
   1. `String str = new String();` 创建一个空字符串对象
   2. `String str = new String("hello world");` 创建一个字符串对象
   3. `String str = new String(char[] value);` 根据字符数组创建一个字符串对象
   4. `String str = new String(byte[] value);` 根据字节数组创建一个字符串对象
3. 方式一存放在**常量池**中，且相同的字符串对象只创建一个
4. 方式二存放在**堆**中，且相同内容可以创建多个对象

#### 处理字符串
| 方法 | 作用 |
| --- | --- |
|`int length()`|获取字符串长度|
|`boolean equals(Object obj)`|字符串内容比较（`==`比较的是字符串地址！）|
|`char charAt(int index)`|获取索引处的字符|
|`char[] toCharArray()`|转换成字符数组|
|`boolean equalsIgnoreCase(String str)`|判断字符串内容是否一致，忽略大小写|
|`String substring(int beginIndex, int endIndex)`|根据索引截取字符串|
|`String substring(int beginIndex)`|从索引处截取字符串|
|`String replace(CharSequence oldChar, CharSequence newChar)`|新值替换旧值|
|`boolean contains(CharSequence s)`|判断字符串中是否包含子串|
|`boolean startsWith(String prefix)`|判断字符串是否以指定字符开头|
|`String[] split(String regex)`|将字符串按某个字符分割为字符串数组|

### ArrayList
| 方法 | 作用 |
| --- | --- |
|`ArrayList<E>()`|创建一个空的集合对象，E为集合中元素的数据类型，可不指定|
|`boolean add(E e)`|添加元素到集合末尾|
|`void add(int index, E element)`|在指定索引处添加元素|
|`E get(int index)`|获取指定索引处的元素|
|`int size()`|获取集合大小|
|`E remove(int index)`|删除指定索引处的元素，返回删除的元素|
|`boolean remove(Object o)`|删除指定元素，返回是否删除成功|
|`E set(int index, E element)`|修改指定索引处的元素，返回修改前的元素|








