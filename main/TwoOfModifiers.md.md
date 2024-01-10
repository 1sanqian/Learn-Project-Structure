# 修饰符(二)

## abstract

+ abstract 修饰符 主要用于定义抽象类和抽象方法
+ abstract 修饰符可用于类、方法、属性、索引和事件
+ 抽象类本身不能被实例化，通常用于定义具有共同特征的一组类的基类，提供一个可供多个派生类共享的通用基类定义，抽象类可以包含抽象方法和具体方法
+ 标记为 抽象的成员 必须由 派生自抽象类 的 非抽象类 来实现

###  抽象属性

1. 抽象属性是在抽象类或接口中声明的属性，抽象类可以包含抽象属性和具体属性，而接口只能包含抽象属性
2. 抽象属性没有具体的实现，只包含属性的声明
3.  抽象属性必须由派生类提供具体实现，派生类使用 override 关键字来实现抽象属性
4. 派生类可以选择性地实现抽象属性
    + 派生类是非抽象类，必须提供对抽象属性的实现
    + 派生类也是抽象类，选择性地将抽象属性留给它的派生类来实现
5. 抽象属性不能使用 [static](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/static) 关键字修饰，因为抽象属性需要由具体的实例提供实现
6. 抽象属性不能是私有的，不能使用 private、protected 或 internal 等访问修饰符，因为它们需要在派生类中被访问和实现

```
public abstract class MyAbstractClass
{
    public abstract int MyAbstractProperty { get; set; } // 抽象属性
    public int MyConcreteProperty { get; set; } // 具体属性
}

public interface IMyInterface
{
    int MyAbstractProperty { get; set; } // 抽象属性
}

public class MyDerivedClass : MyAbstractClass
{
    private int myProperty;

    public override int MyAbstractProperty
    {
        get { return myProperty; }
        set { myProperty = value; }
    }
}
```

 ###  抽象方法

1. 抽象方法是在抽象类或接口中声明的方法，抽象类可以包含抽象方法和具体方法，而接口只能包含抽象方法
2. 由于抽象方法声明不提供实际的实现，因此没有方法主体；方法声明仅以分号结尾，且签名后没有大括号 ({ })
3. 抽象方法必须由派生类提供具体实现，派生类使用 [override](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/override) 关键字来实现抽象方法
4. 派生类可以选择性地实现抽象方法
    + 派生类是非抽象类，必须提供对抽象方法的实现
    + 派生类也是抽象类，选择性地将抽象方法留给它的派生类来实现
5. 抽象方法不能使用 [static](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/static) 关键字修饰，因为抽象方法需要由具体的实例提供实现
6. 抽象方法不能是私有的，不能使用 private、protected 或 internal 等访问修饰符，因为它们需要在派生类中被访问和实现
7. 派生类可以选择使用 new 关键字隐藏基类的抽象方法，但这并不是实现抽象方法的最佳方式，通常应该使用 override 关键字

```
public abstract class MyAbstractClass
{
   public abstract void MyAbstractMethod(); // 抽象方法，没有方法体
   public void MyConcreteMethod() // 具体方法
    {
        // 具体实现
    }
}

public interface IMyInterface
{
    void MyAbstractMethod(); // 抽象方法
}

public class MyDerivedClass : MyAbstractClass
{
    // 1. 抽象方法实现override
    public override void MyAbstractMethod()
    {
        // 具体实现
    }
   // 2. 抽象方法实现 new
    public new void MyAbstractMethod()
    {
        // 具体实现，但并不是最佳实践
    }
}
```

###  抽象类

抽象类具备以下功能：

1. 抽象类不能实例化
2. 抽象类可以包含抽象属性、抽象方法、具体方法、访问器
3. 抽象类 中 的具体方法，具体属性不一定需要在派生类中进行实现，可以有抽象类提供默认实现，或派生类选择性覆盖活继承
3. 一个类包含一个或多个抽象方法，该类必须被声明为抽象类
4. 当一个类派生自一个抽象类并且该抽象类包含抽象方法或属性（包括索引器）时，派生类必须提供对这些抽象成员的具体实现，因为抽象方法和属性是没有实际实现的，它们需要在派生类中被具体实现
5. 若抽象类实现了某个接口，则必须为接口中的所有成员提供实现，因为接口成员默认是抽象的，而抽象类不能包含未实现的抽象成员
6. 抽象类可以通过将接口方法映射到抽象方法上来提供接口成员的实现
7. 无法使用 [sealed](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/sealed) 修饰符来修改抽象类，因为两个修饰符的含义相反。 `sealed` 修饰符阻止类被继承，而 `abstract` 修饰符要求类被继承

```
public interface IShape
{
    double Area { get; }
    int this[int index] { get; set; }
    void Draw();
}

// 抽象类实现接口
public abstract class MyAbstractClass : IShape
{
    private double area;
    private int[] values = new int[3];

    // 抽象属性
    public abstract string ShapeType { get; }

    // 实现接口属性
    public double Area => area;
 
   // 抽象索引器
    public abstract int this[int index] { get; set; }
  
  // 实现接口索引器
    public int this[int index]
    {
        get { return values[index]; }
        set { values[index] = value; }
    }

    // 实现接口方法
    public void Draw()
    {
        Console.WriteLine("Drawing the shape in MyAbstractClass.");
    }

    // 具体方法
    public void Display()
    {
        Console.WriteLine("Displaying the shape.");
    }
 
    // 抽象方法，必须由派生类提供具体实现
    public abstract void AbstractMethod();

    // 抽象访问器
    public abstract string AccessorExample { get; set; }
}

// 派生类
public class MyDerivedClass : MyAbstractClass
{
     // 实现抽象属性
    public override string ShapeType => "Circle";

    // 提供对抽象成员的具体实现
    public override void AbstractMethod()
    {
        Console.WriteLine("Abstract method implemented in MyDerivedClass.");
    }

    // 实现抽象索引器
    public override int this[int index]
    {
        get { return index * 2; }
        set { /* 实现索引器的 set 方法 */ }
    }

   // 实现抽象访问器
    public override string AccessorExample
    {
        get { return "AccessorExample value"; }
        set { Console.WriteLine($"AccessorExample set with value: {value}"); }
    }
}

class Program
{
    static void Main()
    {
        MyDerivedClass derivedInstance = new MyDerivedClass();

        // 使用具体方法
        derivedInstance.Display();  // 输出 "Displaying the shape."

        // 使用实现的接口方法、属性、索引器
        derivedInstance.Draw();  // 输出 "Drawing the shape in MyAbstractClass."
        double area = derivedInstance.Area;
        Console.WriteLine($"Area: {area}");

        derivedInstance[0] = 1;
        derivedInstance[1] = 2;
        derivedInstance[2] = 3;
        Console.WriteLine($"Values: {derivedInstance[0]}, {derivedInstance[1]}, {derivedInstance[2]}");

        // 使用派生类提供的具体实现
        derivedInstance.AbstractMethod();  // 输出 "Abstract method implemented in MyDerivedClass."

        // 使用抽象访问器
        string accessorValue = derivedInstance.AccessorExample;
        Console.WriteLine($"AccessorExample: {accessorValue}");
        derivedInstance.AccessorExample = "New Value"; // 输出 "AccessorExample set with value: New Value"

    }
}
```

### 问题🧐

对于abstract，什么情况下继承base class，什么情况下implement interface，IoC大场景下为什么使用interface

继承 base class： 
+ 定义共享的行为：当有一组类拥有一些共同的行为或属性时，可以将这些公共的行为和属性抽象出来，放到抽象类中，因为抽象类通常用于定义具有共同特征的一组类的基类，使这些类能够继承这个抽象类并共享这些行为
+ 提供默认实现： 抽象类可以包含具体方法，这些方法提供了默认的实现，而派生类可以选择性地覆盖或者继承这些实现
+ 共享状态或字段： 如果一组相关的类需要共享一些状态或字段，抽象类可以在其中定义这些状态，并提供对这些状态的访问

```
public abstract class Animal
{
    // 共享字段
    protected int speed;
    // 定义共享的行为
    public abstract void MakeSound();
    // 提供默认实现
    public void HasName()
    {
        Console.WriteLine("Animals have names");
    }

    public void Accelerate()
    {
        speed += 10;
    }

    public void Brake()
    {
        speed -= 10;
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}

```

implement interface： 
+ 在多继承的情况下：C# 中一个类只能继承自一个直接的基类，但可以实现多个接口，如果抽象类需要具备 某些接口定义 的行为，而同时也需要继承自另一个基类，那么可以通过实现接口来满足多继承的需求
+ 强调规范： 接口提供了一种规范，用于定义类应该具有的方法、属性等。通过实现接口，抽象类可以强调其派生类应该实现某些行为，并确保符合特定的接口规范
+ 提供通用功能： 抽象类可以通过实现接口提供一些通用的功能，这些功能可以在所有派生类中共享。这有助于确保派生类都提供了某些基本的行为，同时允许它们实现各自特定的功能

```
public interface ILogging
{
    void LogMessage(string message);
}

public abstract class BaseClass
{
    // Common functionality for the base class
}
// 多继承
public abstract class LoggingBaseClass : BaseClass, ILogging
{
    public void LogMessage(string message)
    {
        // Log message implementation
    }
}

public interface IShape
{
    bool Authenticate();
    double CalculateArea();
}

public abstract class ShapeBase : IShape
{
   // 强调规范 和 提供通用功能
    public abstract double CalculateArea();
    public bool Authenticate()
    {
        // Authentication logic
        return true;
    }
}
```

IoC场景使用interface:

+ 依赖性： 
    + 在IoC中使用 接口，可以让 类只依赖于接口定义的契约，而不依赖于具体的实现
    + 在IoC中使用 抽象类 ，意味着 派生类 必须实现所有的抽象方法，被迫提供某些默认实现
+ 继承性： 
     +  在IoC中使用 接口，接口允许多个类实现相同的接口，从而实现了多态性，同时一个类可以实现多个接口，IoC 容器能够根据需要选择合适的实现进行注入，提供了更大的灵活性
    + 抽象类在继承上有单一继承的限制，一个类只能继承自一个抽象类。这在一些情况下可能导致设计上的约束，特别是当需要在一个类中使用多个不同的功能或模块时
+ 测试性：  
    +  通过接口，可以更容易地进行单元测试。IoC 容器可以使用模拟对象或者测试实现来替代接口的实际实现，从而隔离和测试系统中的不同部分
    + 抽象类可能包含具体的实现，这使得在进行单元测试时难以使用模拟对象。在进行单元测试时，最好是依赖于接口而不是具体的实现，以便轻松地替换为模拟对象

总的来说，虽然抽象类在某些情况下是有用的，但在 IoC 场景中，由于上述一些限制和缺陷，通常更倾向于使用接口。接口提供了更大的灵活性、松耦合性和可测试性，更适合在 IoC 容器中进行依赖注入，也可以根据具体需求和设计目标选择使用接口还是抽象类，有时甚至两者结合使用

abstract: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/abstract

## const

+ 使用 const 关键字来声明某个常量字段或局部变量
+ const 字段只能在该字段的声明中初始化
+ 常量是一种在程序执行期间其值不能被改变的标识符，常量在编译时被解析，而不是在运行时，意味着常量的值在程序运行前就已经确定，并且不能在运行时修改
+ 常量通常用于定义不会改变的值

```
class Program
{
    // Pi 和 MaxValue 都是常量
    const double Pi = 3.14159;
    
    static void Main()
    {
        const int MaxValue = 100;
        
        Console.WriteLine($"The value of Pi is: {Pi}");
        Console.WriteLine($"The maximum value is: {MaxValue}");
    }
}
```

const: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/const

## event

+ event 关键字用于 声明 发布服务器类中的事件
+ 事件是一种在 类或对象 中定义的 成员，允许 其他类或对象 订阅（或取消订阅）该事件，可以在事件发生时执行特定的操作
+ 事件的声明通常包含两部分：事件的类型和事件处理程序的委托类型
+ 事件的类型通常是一个委托类型，用于指定事件处理程序的方法签名
+ 事件通常在类内的某个地方被触发，即激发事件。这通常通过调用事件的委托来完成
+ 事件是一种特殊的多播委托，仅可以从声明事件的类（或派生类）或结构（发布服务器类）中对其进行调用

下列关键字应用于事件: 

![image.png](https://upload-images.jianshu.io/upload_images/29476859-5e82aef00e28d2ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// 事件数据类
// SampleEventArgs 是一个事件参数类，包含了事件携带的信息
public class SampleEventArgs 
{
    public string Message { get; set; }
}

// 发布者
public class Publisher
{
    // 事件的处理程序委托类型 SampleEventHandler，用于定义事件处理程序的方法签名
    public delegate void SampleEventHandler(object sender, SampleEventArgs e)

    // 声明一个SampleEventHandler 类型的事件
    public event SampleEventHandler SampleEvent;

    // 受保护的虚拟方法，用于触发事件
    protected virtual void RaiseSampleEvent()
    {
        // 在适当的时候触发事件
        SampleEvent?.Invoke(this, new SampleEventArgs { Message = "Event occurred!" });
    }
}

// 订阅者
class Subscriber
{
    // 事件处理程序方法，用于订阅事件
    public void HandleSampleEvent(object sender, SampleEventArgs e)
    {
        Console.WriteLine($"Received message: {e.Message}");
    }
}

class Program
{
    static void Main()
    {
        // 在其他类中创建 Publisher 类的实例
        Publisher publisher = new Publisher();

        // 创建 Subscriber 类的实例
        Subscriber subscriber = new Subscriber();

        // 使用 += 运算符订阅事件
        publisher.SampleEvent += subscriber.HandleSampleEvent;

        // 在适当的时候调用 RaiseSampleEvent 方法，触发事件
        publisher.RaiseSampleEvent();

        // 取消订阅事件
        publisher.SampleEvent -= subscriber.HandleSampleEvent; 
    }
}

// Output
// Received message: Event occurred!
```

event: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/event

## extern

+ extern 修饰符用于声明在外部实现的方法，在当前 C# 代码中并未提供方法的实现，而是该方法的实现在外部的其他语言中
+ extern 修饰符的常见用法是在使用 Interop 服务调入非托管代码时与 DllImport 特性一起使用，同时，还必须将方法声明为 static
+ extern 关键字还可以定义外部程序集别名，使得可以从单个程序集中引用同一组件的不同版本

```
//using System.Runtime.InteropServices;
class ExternTest
{
    // 程序使用从 User32.dll 库导入的 MessageBox 方法, 搭配 static 一起使用
    [DllImport("User32.dll", CharSet=CharSet.Unicode)]
    public static extern int MessageBox(IntPtr h, string m, string c, int type);

    static int Main()
    {
        string myString;
        Console.Write("Enter your message: ");
        myString = Console.ReadLine();
        return MessageBox((IntPtr)0, myString, "My Message Box", 0);
    }
}
```

extern: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/extern

## new 

+ new 关键字可以显式隐藏从基类继承的成员，在派生类中使用 new 关键字可以隐藏基类中具有相同名称的成员，主要用于隐藏基类共享名称的字段、属性、方法、索引器等，但要注意隐藏和覆盖
+ 类或结构中引入的索引器会隐藏具有相同签名的所有基类索引器
+ new 关键字还可用于[创建类型的实例](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/new-operator)或用作[泛型类型约束](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/new-constraint)
+ 同一成员同时使用 new 和 [override](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/override) 是错误的做法，因为这两个修饰符的含义互斥
    + new 修饰符会用同样的名称创建一个新成员并使原始成员变为隐藏
    + override 修饰符会扩展继承成员的实现

```
class BaseClass
{
    public void Display()
    {
        Console.WriteLine("BaseClass Display");
    }
}

class DerivedClass : BaseClass
{
    // 1. 使用 new 关键字隐藏基类的 Display 方法
    public new void Display()
    {
        Console.WriteLine("DerivedClass Display");
    }
}

// 2. 创建实例，实例化类或结构，并为其分配内存
MyClass obj = new MyClass();

// 3. 声明和分配数组，为数组分配内存空间
int[] numbers = new int[5];

// 4. 泛型类型的实例化，new 关键字为类型参数创建新的实例
List<int> myList = new List<int>();

// 5. 在泛型约束中，new() 约束表示泛型类型必须具有公共的无参数构造函数
public class Example<T> where T : new()
{
    // T 必须有无参数构造函数
}
```

**注意： 当 相同名称的新成员 被声明为 不可调用类型 且 基类 将 其声明为一种方法， 则 新引入的成员不会隐藏基类中的同名成员**

```
class BaseClass
{
    public void Method()
    {
        Console.WriteLine("BaseClass Method");
    }
}

class DerivedClass : BaseClass
{
    // 在派生类中引入一个名为 Method 的字段
    // 该字段是一个委托类型，不可调用
    public Action Method;

    public new void Method()
    {
        Console.WriteLine("DerivedClass Method");
    }
}

class Program
{
    static void Main()
    {
        DerivedClass derivedInstance = new DerivedClass();

        // 调用基类的方法
        derivedInstance.Method();  // 输出 "BaseClass Method"

        // 调用派生类中引入的字段（不可调用）
        derivedInstance.Method?.Invoke();  // 仅编译通过，但实际上不会执行任何操作
    }
}
```

new: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/new-modifier

## override

+ override 关键字用于表示一个方法、属性、索引器或事件在派生类中重写基类中的同名成员
+ override 方法提供从基类继承的方法的新实现，通过 override 声明重写的方法称为重写基方法
    + override 方法必须具有与重写基方法相同的签名（名称、返回类型、参数列表等）
    + override 方法支持协变返回类型
    + 不能重写非虚方法或静态方法
    + 重写基方法必须是 virtual、abstract 或 override
+ override 声明不能更改 virtual 方法的可访问性
+ 不能使用 new、static 或 virtual 修饰符修改 override 方法
+ 重写属性声明必须指定与继承的属性完全相同的访问修饰符、类型和名称
     + 只读重写属性支持协变返回类型
     + 重写属性必须为 virtual、abstract 或 override

```
class BaseClass
{
   public virtual int Value
    {
        get { return 42; }
    }

    public virtual void Display()
    {
        Console.WriteLine("BaseClass Display");
    }

    public virtual int this[int index]
    {
        get { return index * 2; }
    }

    public virtual event EventHandler MyEvent;
}

class DerivedClass : BaseClass
{
   // 1. 属性的重写：使用 override 修饰符重写基类中的属性
    public override int Value
    {
        get { return 10; }
    }
    // 2. 方法的重写 ：使用 override 修饰符重写基类中的方法
    public override void Display()
    {
        Console.WriteLine("DerivedClass Display");
    }
   // 3. 索引器的重写：使用 override 修饰符重写基类中的索引器
    public override int this[int index]
    {
        get { return index * 10; }
    }
    // 4. 事件的重写：使用 override 修饰符重写基类中的事件
    public override event EventHandler MyEvent;
}


```

override: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/override

## readonly

+   readonly 关键字用于声明只读字段。只读字段是在声明时或同一个类的构造函数中被赋值，并且在其生命周期内不可更改的字段，一旦赋值，字段的值在之后不能再被修改
      + 在声明中初始化变量时赋值
      + 在包含实例字段声明的类的实例构造函数中赋值
      + 在包含静态字段声明的类的静态构造函数中赋值
      + 构造函数退出后，不能分配 readonly 字段。 此规则对于值类型和引用类型具有不同的含义：
         + 由于值类型直接包含数据，因此属于 readonly 值类型的字段不可变
         + 由于引用类型包含对其数据的引用，因此属于 readonly 引用类型的字段必须始终引用同一对象，该对象是可变的， readonly 并不能阻止通过引用类型的实例修改实例内部的数据
+ 使用 readonly 修饰符来声明实例成员不会修改结构的状态
+ 在 readonly struct 类型定义中，[readonly 指示结构](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/struct#readonly-struct)类型是不可变的

+ 在结构类型内的实例成员声明中，readonly 指示实例成员不修改结构的状态
+ 在 [ref readonly ](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/readonly#ref-readonly-return-example)方法返回实现对只读字段的引用，只允许在只读字段上创建只读引用，而不违反字段的只读性

```
// 即使在类构造函数中给字段 MyReadOnlyField 赋了值，也无法在方法 TestChange 中更改其值
public class MyClass
{
    // 只读字段
    public readonly int MyReadOnlyField;

    // 构造函数中初始化只读字段
    public MyClass(int value)
    {
        MyReadOnlyField = value;
    }

    // 尝试在其他方法中修改只读字段（编译错误）
    public void TestChange()
    {
        // 下面这行会导致编译错误：
        // MyReadOnlyField = 42;
    }
    // 方法返回只读字段的只读引用
    public ref readonly int GetReadOnlyField()
    {
        return ref MyReadOnlyField;
    }
}
// 使用 readonly 修饰符来声明实例成员不会修改结构的状态
public readonly double Sum()
{
    return X + Y;
}

// ref readonly
class Program
{
    static void Main()
    {
        // 创建 MyClass 实例
        MyClass myInstance = new MyClass(42);

        // 获取只读字段的只读引用
        ref readonly int readOnlyRef = ref myInstance.GetReadOnlyField();

        // 通过只读引用访问只读字段
        Console.WriteLine($"Value of MyReadOnlyField: {readOnlyRef}");
    }
}
```

**readonly 字段只能在构造函数中进行赋值，并且只有在构造函数上下文中，将 readonly 字段作为 out 或 ref 参数传递才有效**

```
public class MyClass
{
    // 只读字段
    public readonly int MyReadOnlyField;

    // 构造函数中初始化只读字段
    public MyClass(int value)
    {
        MyReadOnlyField = value;

        // 将只读字段作为 out 参数传递给方法
        MethodWithOutParameter(out MyReadOnlyField);
    }

    // 方法接收只读字段作为 out 参数
    private void MethodWithOutParameter(out int value)
    {
        // 这里可以使用 value，因为它是 out 参数
        value = 100;
    }
}
// 在构造函数上下文中，readonly 字段的赋值是允许的
// 在其他上下文中, 尝试修改 readonly 字段的值将导致编译错误
```

**备注:**
+ readonly 关键字不同于 [const](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/const) 关键字，const 字段只能在该字段的声明中初始化
+ 可以在字段声明和任何构造函数中多次分配 readonly 字段
+ 根据所使用的构造函数，readonly 字段可能具有不同的值
+ const 字段是编译时常量， readonly 字段是可用于运行时常量

```
public class SamplePoint
{
    public int x;
    // Initialize a readonly field
    public readonly int y = 25;
    public readonly int z;

    public SamplePoint()
    {
        // Initialize a readonly instance field
        z = 24;
    }
    // 根据所使用的构造函数，readonly 字段可能具有不同的值
    public SamplePoint(int p1, int p2, int p3)
    {
        x = p1;
        y = p2;
        z = p3;
    }

    public static void Main()
    {
        SamplePoint p1 = new SamplePoint(11, 21, 32);   // OK
        Console.WriteLine($"p1: x={p1.x}, y={p1.y}, z={p1.z}");
        SamplePoint p2 = new SamplePoint();
        p2.x = 55;   // OK
        Console.WriteLine($"p2: x={p2.x}, y={p2.y}, z={p2.z}");
    }
    /*
     Output:
        p1: x=11, y=21, z=32
        p2: x=55, y=25, z=24
    */
}
```

readonly: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/readonly

## sealed

+ sealed 关键字用于阻止其他类继承某个类或结构，或者阻止某个方法被子类重写，防止派生类替代基类的特定虚方法或属性
+ 将 abstract 修饰符与 密封类 结合使用是错误的，抽象类必须由提供抽象方法或属性的实现的类来继承，而密封类 会阻止 子类的重写和实现， 
+ sealed 阻止重写的基类方法或属性必须是 virtual
+ 应用于方法或属性时，sealed 修饰符必须始终与 override 结合使用，因为结构是隐式密封的，所以无法继承它们

```
// 1. 阻止类的继承 ：防止其他类继承指定的类或结构
public sealed class SealedClass
{
    // 类的成员和方法
}

// 2. 阻止方法的重写：阻止子类重写指定的虚拟方法或抽象方法
public class BaseClass
{
    // 虚拟方法
    public virtual void MyMethod()
    {
        // 实现
    }
}

public class DerivedClass : BaseClass
{
    // 使用 sealed 阻止重写
    public sealed override void MyMethod()
    {
        // 子类的实现
    }
}
```

sealed:https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/sealed

## static

+ static 修饰符用于声明静态成员，静态成员属于类而不是类的实例
+ static 修饰符可用于声明 static 类，若 static 关键字应用于类，则类的所有成员都必须为 static
+ 可以将 static 修饰符添加到字段、方法、属性、运算符、事件和构造函数
+ 类、接口和 static 类可以具有 static 构造函数
+ static 修饰符不能用于索引器或终结器
+ static 修饰符添加到 [本地函数](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/local-functions)，静态本地函数无法捕获局部变量或实例状态
+ static 修饰符添加到 [Lambda 表达式](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/lambda-expressions) 或 [匿名方法](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/delegate-operator)， 静态Lambda 表达式或匿名方法无法捕获局部变量或实例状态
+ 不可以使用 [this](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/this) 引用 static 方法或属性访问器

```
// 1. 声明静态类且只含静态方法，静态字段
static class CompanyEmployee
{
     public static int StaticField = 42;
    public static void DoSomething() { /*...*/ }
    public static void DoSomethingElse() { /*...*/  }
}

// 2. 常数或类型声明是隐式的 static 成员，不能通过实例引用 static 成员，但可以通过类型名称引用
public class MyBaseC
{
    public struct MyStruct
    {
        public static int x = 100;
    }
}
// 注意：要引用 static 成员 x，除非可从相同范围访问该成员，否则请使用完全限定的名称 MyBaseC.MyStruct.x
Console.WriteLine(MyBaseC.MyStruct.x);

// 3. 静态构造函数： 使用 static 声明的构造函数是类级别的构造函数，在类被使用之前执行一次
public class MyClass
{
    static MyClass()
    {
        Console.WriteLine("Static constructor called.");
    }
}

// 4.静态初始化
// 使用尚未声明的 static 字段来初始化另一个 static 字段, 在向 static 字段显式赋值之后才会定义结果
class Test
{
    static int x = y;
    static int y = 5;

    static void Main()
    {
        Console.WriteLine(Test.x);
        Console.WriteLine(Test.y);

        Test.x = 99;
        Console.WriteLine(Test.x);
    }
}
/*
Output:
    0
    5
    99
*/
```

使用静态字段和方法的情况举例：
+ 假定此类包含计数员工的方法 AddEmployee（）和存储员工人数的字段 employeeCounter
+  AddEmployee（）方法和 employeeCounter 字段均不属于任何一个员工实例， 相反，它们属于全体员工这个类。 应将其声明为该类的 static 成员

```
public class Employee4
{
    public string id;
    public string name;

    public Employee4()
    {
    }

    public Employee4(string name, string id)
    {
        this.name = name;
        this.id = id;
    }

    public static int employeeCounter;

    public static int AddEmployee()
    {
        return ++employeeCounter;
    }
}

class MainClass : Employee4
{
    static void Main()
    {
        Console.Write("Enter the employee's name: ");
        string name = Console.ReadLine();
        Console.Write("Enter the employee's ID: ");
        string id = Console.ReadLine();

        // Create and configure the employee object.
        Employee4 e = new Employee4(name, id);
        Console.Write("Enter the current number of employees: ");
        string n = Console.ReadLine();
        Employee4.employeeCounter = Int32.Parse(n);
        Employee4.AddEmployee();

        // Display the new information.
        Console.WriteLine($"Name: {e.name}");
        Console.WriteLine($"ID:   {e.id}");
        Console.WriteLine($"New Number of Employees: {Employee4.employeeCounter}");
    }
}
/*
Input:
Matthias Berndt
AF643G
15
 *
Sample Output:
Enter the employee's name: Matthias Berndt
Enter the employee's ID: AF643G
Enter the current number of employees: 15
Name: Matthias Berndt
ID:   AF643G
New Number of Employees: 16
*/
```

static : https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/static

## unsafe

+ unsafe 关键字表示不安全上下文，允许在不安全上下中使用指针和直接内存访问

```
// 1. 指针声明和使用
unsafe
{
    int x = 10;
    int* ptr = &x;
    Console.WriteLine(*ptr); // 通过指针访问变量的值
}
// 2. 固定语句: fixed 语句用于创建指向托管变量的指针，以便在不受垃圾回收器干扰的情况下进行内存操作
unsafe
{
    fixed (int* ptr = &array[0])
    {
        // 使用 ptr 操作数组
    }
}
// 3. 指针算术
unsafe
{
    int[] array = { 1, 2, 3, 4, 5 };
    fixed (int* ptr = &array[0])
    {
        for (int i = 0; i < 5; i++)
        {
            Console.WriteLine(*(ptr + i)); // 指针算术
        }
    }
}
// 4. 结构固定
struct MyStruct
{
    public int X;
    public int Y;
}

unsafe
{
    MyStruct myStruct;
    fixed (int* ptrX = &myStruct.X, ptrY = &myStruct.Y)
    {
        // 使用指针访问结构的字段
    }
}
```

unsafe:https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/unsafe

## virtual

+ virtual 关键字用于修改方法、属性、索引器或事件声明，并使它们可以在派生类中被重写，在面向对象编程中，这是实现多态性的一种方式
+ 虚拟成员的实现可由派生类中的[替代成员](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/override)更改
+ 调用虚拟方法时，将为替代的成员检查该对象的运行时类型：会调用大部分派生类中的该替代成员，如果没有派生类替代该成员，则它可能是原始成员
+ 默认情况下，方法是非虚拟的，不能替代非虚方法
+ 静态属性上使用 virtual 修饰符是错误的
+ 通过包括使用 override 修饰符的属性声明，可在派生类中替代虚拟继承属性

```
public class MyBaseClass
{
    // 1. 虚属性：使用 virtual 关键字声明虚属性
    public virtual int MyProperty { get; set; }
    // 2. 虚方法：使用 virtual 关键字声明虚方法
    public virtual void MyMethod()
    {
        Console.WriteLine("Base class method");
    }
}

public class MyDerivedClass : MyBaseClass
{
    // 使用 override 关键字重写基类的虚属性
    public override int MyProperty
    {
        get { return base.MyProperty * 2; }
        set { base.MyProperty = value; }
    }
    // 使用 override 关键字重写基类的虚方法
    public override void MyMethod()
    {
        Console.WriteLine("Derived class method");
    }
}
```

virtual：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/virtual

## volatile

+ volatile 是一个关键字，用于指示一个字段可能会被多个线程同时访问，因此在访问该字段时不应该进行编译器优化或缓存
+ volatile 修饰的字段告诉编译器不要对这个字段进行优化，而是直接从内存中读取或写入这个字段的值
+ volatile 关键字可应用于以下类型的字段：
    +  引用类型
    +  指针类型（在不安全的上下文中）。 请注意，虽然指针本身可以是可变的，但是它指向的对象不能是可变的。 换句话说，不能声明“指向可变对象的指针”
    + 简单类型，如 `sbyte`、`byte`、`short`、`ushort`、`int`、`uint`、`char`、`float` 和 `bool`。
    + 具有以下基本类型之一的 `enum` 类型：`byte`、`sbyte`、`short`、`ushort`、`int` 或 `uint`。
    + 已知为引用类型的泛型类型参数
    + [IntPtr](https://learn.microsoft.com/zh-cn/dotnet/api/system.intptr) 和 [UIntPtr](https://learn.microsoft.com/zh-cn/dotnet/api/system.uintptr)
+ volatile 关键字只能应用于 class 或 struct 的字段。 不能将局部变量声明为 volatile

```
using System;
using System.Threading;

public class SharedResource
{
    // 使用 volatile 关键字声明共享字段
    public volatile int sharedValue = 0;
}

class Program
{
    static void Main()
    {
        SharedResource sharedResource = new SharedResource();

        // 创建一个线程修改共享字段的值
        Thread modifyThread = new Thread(() =>
        {
            for (int i = 0; i < 10; i++)
            {
                // 修改共享字段的值
                sharedResource.sharedValue = i;
                Thread.Sleep(500); // 模拟耗时操作
            }
        });

        // 创建另一个线程读取共享字段的值
        Thread readThread = new Thread(() =>
        {
            while (sharedResource.sharedValue < 9)
            {
                // 读取共享字段的值
                Console.WriteLine($"Shared Value: {sharedResource.sharedValue}");
                Thread.Sleep(1000); // 模拟耗时操作
            }
        });

        // 启动两个线程
        modifyThread.Start();
        readThread.Start();

        // 等待两个线程完成
        modifyThread.Join();
        readThread.Join();
    }
}
```

volatile：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/volatile
