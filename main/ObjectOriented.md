# 面向过程 VS 面向对象
## 面向过程

+ 以 过程 为中心 的编程思想，在编程时把 解决问题的 步骤 分解出来，使用函数实现这些步骤，再一步步按顺序调用函数，直至解决问题
+ 按照面向过程的思想，在解决问题时，各函数之间是为了问题服务
+ 面向过程思想偏向于我们做一件事的流程，首先做什么，其次做什么，最后做什么

### 特性

自顶向下、逐步求精、模块化

![image.png](https://upload-images.jianshu.io/upload_images/29476859-59e5dffdb3445eae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 面向对象

+ 以 对象 为中心的编程思想，在编程时把要 解决的问题 抽象成 各个对象
+ 建立对象的目的 不是为了完成一个步骤，而是为了描叙某个对象在整个解决问题的步骤中的属性和行为
+ 按照面向对象的思想，是站在提出问题的主体的角度去解决问题，可将解决这一具体问题的步骤，抽象为解决这一类问题的方法，以后再遇到类似的问题，可以复用解决思路
+ 面向对象是按人们认识客观世界的系统思维方式，采用基于对象（实体）的概念建立模型，模拟客观世界分析、设计、实现软件的方法
+ 通过面向对象的理念，将计算机软件系统能与现实世界中的系统一一对应

### 特性

封装、继承、多态、抽象

![image.png](https://upload-images.jianshu.io/upload_images/29476859-ecd80e12178aa1a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 举例

需求： 写一道 蛋炒饭 的程序

蛋炒饭 和 盖浇饭

蛋炒饭： 有一道工序是把 鸡蛋 和 米饭混合一起炒

盖浇饭： 点什么菜，最后就把 这道菜 和 米饭混合一起炒

如果用 面向过程 的思想，写出了程序最后就是 一份 具体 的蛋炒饭

如果用 面向对象 的思想，可以根据 需要 写出 需要的 炒饭，可以是 蛋炒饭/红烧肉盖饭/青椒土豆盖浇饭

根据 面向过程的思想，就是只 做一道 蛋炒饭 的程序

根据 面向对象的思想，把 菜 和 饭 作为 对象 抽离 出来，根据需求 输入 相应 的菜 和饭，就能得到 对应 的 炒饭程序

## 面向对象编程的特征

面向对象编程的四项基本原则为：
+ 抽象：抽象是简化复杂系统的过程，将实体的相关特性和交互建模为类，以定义系统的抽象表示。抽象通过抽象类和接口来实现，抽象类定义了一些具体和一些抽象的方法，而接口定义了一组方法的契约，由实现类提供具体实现
+ 封装： 封装是将对象的状态（数据）和行为（方法）捆绑在一起，隐藏对象的内部状态和功能，通过使用访问修饰符，控制允许通过一组公共成员/函数进行访问
+ 继承：继承是一种机制，允许一个类（派生类）使用另一个类（基类）的属性和方法，同时继承也支持创建类的层次结构，通过在派生类中添加、修改或隐藏成员，实现对基类的扩展
+ 多态： 多态是一种通过同一接口（方法名）来表示不同实现的机制，多态通常通过方法的重写（override）和接口实现来实现

### 抽象

+ 抽象是通过隐藏不必要的细节，突出重点的特征，将实体抽象为类
+ 抽象出来的类可以使用 abstract 关键字 来定义类

将计算图形面积 的共性 抽象成 类 Shape，使用具体实现类实现面积计算
```
// 抽象类
public abstract class Shape
{
    // 抽象方法，用于计算图形的面积
    public abstract double CalculateArea();
}
// Circle 是 具体的实现类 
public class Circle : Shape
{
    // 圆形的半径
    public double Radius { get; set; }

    // 实现 CalculateArea 方法： 计算圆型的面积
    public override double CalculateArea()
    {
        return Math.PI * Radius * Radius;
    }
}
// Rectangle 是 具体的实现类 
public class Rectangle : Shape
{
    // 矩形的长和宽
    public double Length { get; set; }
    public double Width { get; set; }

    // 实现 CalculateArea 方法： 计算矩形的面积
    public override double CalculateArea()
    {
        return Length * Width;
    }
}
```

### 封装

+ 封装是将数据和方法封装在同一个单元中
+ 通过定义公共接口（公共方法）来控制对数据的访问
+ 封装是为了防止外部直接访问数据，防止内部数据被随意的定义和修改，保障数据的安全性

![image.png](https://upload-images.jianshu.io/upload_images/29476859-fe5e20d3035a3635.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
public class Person
{
    // 封装的字段，私有字段只能在类内部访问
    private string name;
    private int age;

    // 封装的属性，提供对私有字段的访问和修改
    public string Name
    {
        get { return name; }
        set { name = value; }
    }

    public int Age
    {
        get { return age; }
        set
        {
            // 对年龄进行一些验证
            if (value >= 0 && value <= 120)
            {
                age = value;
            }
            else
            {
                Console.WriteLine("Invalid age value");
            }
        }
    }

    // 公共方法，对外提供访问和操作封装字段的接口
    public void DisplayInfo()
    {
        Console.WriteLine($"Name: {Name}, Age: {Age}");
    }
}

class Program
{
    static void Main()
    {
        // 创建 Person 对象
        Person person1 = new Person();

        // 设置对象的属性值
        person1.Name = "John";
        // 设置 Age 的值， 但是 外界对于 Age 的一些验证并不知情
        person1.Age = 25;

        // 调用方法显示信息
        person1.DisplayInfo();
    }
}
```

### 继承

+ 基于某一个类进行扩展，使一个新类直接拥有基类的基本特征，而不需要重复去写，这就是继承的思想
+ C# 中，类只能继承一个类，一个子类只能有一个父类，一个父类可以有多个子类，若要实现多重继承，可以采用接口的形式
+ 在 C# 中，类默认继承自 System.Object 类,直接继承的是直接父类，即使没有显式指定某个类的基类，它仍然隐式地继承自 System.Object
+ C#中，使用 class 关键字来定义一个类，使用  `:`(冒号) 来表示继承关系

```
class Animal
{
    public void Eat()
    {
        Console.WriteLine("Animal is eating");
    }
}
// Mammal 类继承 Animal
class Mammal : Animal
{
    public void Sleep()
    {
        Console.WriteLine("Mammal is sleeping");
    }
}
// Dog 的直接父类是 Mammal，间接父类  Animal
class Dog : Mammal
{
    public void Bark()
    {
        Console.WriteLine("Dog is barking");
    }
}
```

### 多态

+ 多态是一种通过同一接口（方法名）来表示不同实现的机制
+ 多态性有两种主要形式：编译时多态性和运行时多态性
+ 编译时多态性（静态多态性）是编译时，通常与方法的重载（overloading）有关，同一个类中有多个方法具有相同的名称但不同的参数列表
+ 运行时多态性（动态多态性）是运行时，通常与方法的重写（overriding）有关，子类可以提供对基类方法的新实现

```
class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Generic animal sound");
    }
}

class Dog : Animal
{
    // 重写
    public override void MakeSound()
    {
        Console.WriteLine("Woof! Woof!");
    }

    public void Gender(int x)
    {
        if(x==1)
         {
          Console.WriteLine("Gender: 男");
         }
         else if (x==2)
         {
           Console.WriteLine("Gender: 女");
         }
         else
         {
         Console.WriteLine("Gender: 不明确");
         }
    }
    // 重载
    public void Gender(string s)
    {
        Console.WriteLine("Gender： " + s);
    }

}

class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}

class Program
{
    static void Main()
    {
        Animal animal = new Dog();
        animal.MakeSound();  // 运行时确定调用 Dog 类中的 MakeSound 方法
        animal.Gender(1);   // 编译时确定调用 Dog 类中的 Gender(int x)方法
        animal.Gender("男")  // 编译时确定调用 Dog 类中的 Gender(string x)方法
        animal = new Cat();
        animal.MakeSound();  // 运行时确定调用 Cat 类中的 MakeSound 方法
    }
}
```

#### 方法的重载

方法的重载是指在同一个类中，可以定义多个具有相同名称但参数列表不同的方法
+ 重载的方法具有相同的方法名
+ 参数列表必须不同，包括参数的类型、个数或顺序
+ 返回类型可以相同也可以不同

```
class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }

    public double Add(double a, double b)
    {
        return a + b;
    }

    public string Add(string a, string b)
    {
        return a + b;
    }
}
```

#### 方法的重写

方法的重写是指在派生类中重新实现（覆盖）基类中已有的方法
+ 重写的方法具有相同的方法签名（方法名、参数列表、返回类型），并且在基类中必须是虚拟（virtual）或抽象（abstract）的
+ 使用 override 关键字进行重写

```
class Shape
{
    public virtual void Draw()
    {
        Console.WriteLine("Drawing a shape");
    }
}

class Circle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("Drawing a circle");
    }
}
```

#### 重写和重载的区别

##### 方法名称

+ 重载：方法名相同，参数列表不同
+ 重写：方法名相同，参数列表相同

##### 发生位置

+ 重载：在同一个类中
+ 重写：在派生类中

##### 关键字

+ 重载：使用相同的方法名，不需要额外的关键字
+ 重写：使用 override 关键字

##### 调用方式

+ 重载：编译器根据调用时的参数类型、个数、顺序选择合适的方法
+ 重写：运行时根据对象的实际类型选择调用的方法

##### 用途

+ 重载：提供更灵活的方法调用，可以根据不同的参数执行不同的逻辑
+ 重写：实现多态，允许派生类提供特定于其类型的实现
