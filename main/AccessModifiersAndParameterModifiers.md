# 修饰符（一）

在 C# 中，修饰符（modifiers）是用于修改类型、成员、方法等声明的关键字，它们提供了额外的信息和控制

C# 中，修饰符主要包括 访问修饰符、参数修饰符、其他修饰符等

## 访问修饰符

访问修饰符是关键字，用于指定成员或类型已声明的可访问性

访问修饰符主要是用于控制 类、成员、方法等的访问权限，包括:
+  [`public`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/public)
+ [`private`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private)
+ [`protected`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected)
+ [`internal`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/internal)
+ [`protected internal`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected-internal)
+ [`private protected`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private-protected)
+ [`file`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/file)


### 默认可访问性规则 

对于顶级类型（类、结构、接口等），默认是 internal（在同一程序集内可见）；对于成员（字段、方法等），默认是 private（仅在类内可见）

```
// 默认情况下，顶级类型是 internal
class MyClass
{
    // 这里的类成员默认是 private
    int myField;

    // 默认情况下，成员是 private
    void MyMethod() { }
}

// 明确指定类的可访问性为 public
public class MyPublicClass
{
    // 这里的类成员默认是 private
    int myField;

    // 默认情况下，成员是 private
    void MyMethod() { }
}

class AnotherClass
{
    // 明确指定成员的可访问性为 public
    public int MyPublicField;

    // 明确指定成员的可访问性为 public
    public void MyPublicMethod() { }
}
```

### 可访问级别

如图所示，访问修饰符的可访问权限由上到下依次变小

![image.png](https://upload-images.jianshu.io/upload_images/29476859-9984bf832f3e6376.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意：** 
+ 除使用 protected internal 或private protected 组合的情况外，一个成员或类型仅允许一个访问修饰
+ 命名空间中不允许出现访问修饰符， 命名空间没有任何访问限制
+ 其他类型的成员的嵌套类型可以具有如下图所示的可访问性
+ 具有 private 可访问性的 interface 成员必须具有默认的实现

![image.png](https://upload-images.jianshu.io/upload_images/29476859-1671ce1d4fe9d956.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ 访问限制

![image.png](https://upload-images.jianshu.io/upload_images/29476859-0f7f1271bda51b6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ 如果使用 [`record`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/record) 关键字修饰符修改类或结构，则允许相同的访问修饰符；
此外，使用 [`record`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/record) 修饰符，类和结构的默认成员可访问性仍然为 `private`

```
public record Person
{
    // record 类型的默认行为是将成员的可访问性设置为 private
    string FirstName { get; init; }
    string LastName { get; init; }
    // 默认情况下，record 类型的成员可访问性是 private , 但仍可以显式指定访问修饰符，如public
    public int Ago {get; init;}

    // 自定义构造函数
    public Person(string firstName, string lastName, int ago)
    {
        FirstName = firstName;
        LastName = lastName;
        Ago = ago;
    }
}

class Program
{
    static void Main()
    {
        // 创建记录类型实例
        Person person = new Person("John", "Doe",18);

        // 访问记录类型的属性，因为属性的可访问性是 private
        Console.WriteLine($"First Name: {person.FirstName}, Last Name: {person.LastName}, Ago:{person.ago}");
    }
}
```

+ 嵌套类型的可访问性依赖于它的[可访问域](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/accessibility-domain)，该域是由已声明的成员可访问性和直接包含类型的可访问域这二者共同确定的。 但是，嵌套类型的可访问域不能超出包含类型的可访问域

```
// 外部类型，有一个嵌套类型 NestedClass
public class OuterClass
{
    // 外部类型的成员，默认是 private
    private int outerField;

    // 外部类型的成员方法，默认是 private
    private void OuterMethod() { }

    // 嵌套类型，默认可访问性取决于外部类型的可访问性
    public class NestedClass
    {
        // 嵌套类型的成员，可以访问外部类型的成员
        public void AccessOuterMembers(OuterClass outer)
        {
            // 可以访问外部类型的 private 成员
            outer.outerField = 42;

            // 可以调用外部类型的 private 方法
            outer.OuterMethod();
        }
    }
}

class Program
{
    static void Main()
    {
        // 外部类型的实例
        OuterClass outerInstance = new OuterClass();

        // 嵌套类型的实例
        OuterClass.NestedClass nestedInstance = new OuterClass.NestedClass();

        // 在 Main 方法中，无法直接访问嵌套类型的成员
        // 这是因为嵌套类型的可访问域不能超过外部类型的可访问域
        // 下面的代码将无法通过编译
        // nestedInstance.AccessOuterMembers(outerInstance);
    }
}
```

### 关键字

#### 1. public 

+ public 公共访问是允许的最高访问级别，对访问公共成员没有限制

```
class PointTest
{
    public int x;
    public int y;
}

class Program
{
    static void Main()
    {
        var p = new PointTest();
        // 直接从 Program 访问 PointTest 的公共成员 x 和 y
        p.x = 10;
        p.y = 15;
        Console.WriteLine($"x = {p.x}, y = {p.y}");
    }
}
// Output: x = 10, y = 15

// 如果将 `public` 访问级别更改为 private 或 protected，则会收到错误消息：
// “xxx”不可访问，因为它受保护级别限制。
```

*public：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/public*

#### 2. private
 
+ 私有访问是允许的最低访问级别。 私有成员只有在声明它们的类和结构体中才是可访问的
+ 同一体中的嵌套类型也可以访问那些私有成员
+ 在声明私有成员的类或结构外引用它会导致编译时错误

```
class Employee2
{
    private readonly string _name = "FirstName, LastName";
    private readonly double _salary = 100.0;

    public string GetName()
    {
        return _name;
    }

    public double Salary
    {
        get { return _salary; }
    }
}

class PrivateTest
{
    static void Main()
    {
        var e = new Employee2();

        // 成员变量是 private
        // 所以不能进行如下访问
        //    string n = e._name;
        //    double s = e._salary;

        // 通过方法间接访问
        string n = e.GetName();
        double s = e.Salary;
    }
}
```

*private：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private*

#### 3. protected

+ 受保护成员在其所在的类中可由派生类实例访问
+只有在通过派生类类型进行访问时，基类的受保护成员在派生类中才是可访问的

```
class A
{
    protected int x = 123;
}

class B : A
{
    static void Main()
    {
        var a = new A();
        var b = new B();

        // 不可以通过 A 的实例 a 访问 保护属性 x
        // a.x = 10;

        // 可以通过 A 的派生类 b 访问 包含保护属性 x
        b.x = 10;
    }
}
```

*protected：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected*

#### 4. internal

+ 只有在同一程序集的文件中，内部类型或成员才可访问
+ 内部访问通常用于基于组件的开发，因为它可使一组组件以私有方式进行协作，而不必向应用程序代码的其余部分公开
+ 从定义具有内部访问权限的类型或成员的程序集外部引用该类型或成员是错误的

```
// 程序集 Assembly1.cs  
internal class BaseClass
{  
   public static int intM = 0;  
}  

// 程序集 Assembly1.cs    
public class BaseClass2
{  
   internal static int intM = 0;  
}  

//程序集 Assembly1_a.cs  
class TestAccess
{  
   static void Main()
   {  
      // 1. 
      // BaseClass 的程序集来源是 Assembly1
      // TestAccess 的程序集来源是 Assembly1_a  
      // TestAccess 相对于BaseClass 来说是外部程序集，无法直接访问 
      var myBase = new BaseClass();   // 报错

      // 2. 
      // 不同程序集下：
      // 可以实例化 BaseClass2类，但不能访问内部成员 intM
      var myBase = new BaseClass2();  // 成功
      BaseClass.intM = 444;    // 报错  
   }  
}  
```

*internal：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/internal*

#### 5. protected internal

+ protected internal 关键字组合是一种成员访问修饰符
+ 可从 当前程序集 或 派生自包含类 的类型 访问 受保护的内部成员
+ 通过 派生类 访问 受保护的内部成员，前提 通过 派生类类型 的 变量 进行 访问
+ **结构成员不能为 protected internal，因为无法继承结构**

```
// 程序集 Assembly1.cs
public class BaseClass
{
   protected internal int myValue = 0;
}

// 程序集 Assembly1.cs
class TestAccess
{
    void Access()
    {
        // 相同程序集，TestAccess 可以通过new BaseClass 访问 受保护内部成员  myValue
        var baseObject = new BaseClass();
        baseObject.myValue = 5;
    }
}

// 程序集Assembly2.cs
class DerivedClass : BaseClass
{
    static void Main()
    {
        var baseObject = new BaseClass();
        var derivedObject = new DerivedClass();

        // 不同程序集，无法通过 new  BaseClass 的实例去访问 受保护内部成员 myValue
        baseObject.myValue = 10; // 报错

        // 不同程序集，通过 派生 DerivedClass 的实例 去访问 受保护的内部成员 myValue
        derivedObject.myValue = 10;  // ok
    }
}
```

*protected internal：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/protected-internal*

#### 6.private protected

+ private protected 关键字组合是一种成员访问修饰符(C# 版本 7.2 及更高版本中有效)
+ 仅派生自包含类的类型可访问私有受保护成员，但仅能在其包含程序集中访问
+ **结构成员不能为 private protected，因为无法继承结构**

```
// 程序集 Assembly1.cs
public class BaseClass
{
    private protected int myValue = 0;
}

// 程序集 Assembly1.cs
public class DerivedClass1 : BaseClass
{
    void Access()
    {
        var baseObject = new BaseClass();

        // 同一程序集下： 私有受保护成员  myValue 不能通过 BaseClass 的实例访问，只能 BaseClass 类内部访问或者 BaseClass 的派生类访问
        baseObject.myValue = 5; // 报错

        // DerivedClass1 是 BaseClass 的派生类，可以直接访问myValue
        myValue = 5;  // ok
    }
}

// 程序集 Assembly2.cs
class DerivedClass2 : BaseClass
{
    void Access()
    {
        //不同一程序集下：无法访问 其他程序集的  私有受保护 成员
        myValue = 10;  //  报错
    }
}
```

*private protected：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/private-protected*

#### 7. file

+ file 上下文关键字是类型修饰符
+ file 修饰符将 顶级类型的范围 和 可见性限制 为 声明它的 文件
+ file 修饰符通常应用于源生成器编写的类型
+ 嵌套在 file类型 中的 任何类型 也仅在声明 它的文件 中可见,程序集中的其他类型可以使用与 file类型相同的名称
+  file 类型不能是可见性超过 file 范围的任何成员的返回类型或参数类型

```
// In File1.cs:
file interface IWidget
{
    int ProvideAnswer();
}

file class HiddenWidget
{
    public int Work() => 42;
}
public class Widget : IWidget
{
    public int ProvideAnswer()
    {
        var worker = new HiddenWidget();
        return worker.Work();
    }
}

// In File2.cs:
// 可以声明与文件本地类型具有相同名称的类型, 文件本地类型不可见
// 与HiddenWidget不冲突
public class HiddenWidget
{
    public void RunTask()
    {
        // omitted
    }
}
```

*file： https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/file*

## 参数修饰符

+ 参数修饰符 主要用于改变方法参数行为或传递方式的 关键字
+ 可以用于方法的参数列表中
+ 提供了不同的传递方式或指定了参数的特殊行为
+ 常见的参数修饰符有：
    + in 修饰符
    + out 修饰符
    + ref 修饰符
    + params 修饰符


### 1. in

#### in 作为修饰符

+ in 修饰符用于指定参数是只读的(C# 7.2 引入的一个特性)
+ 使用 in 修饰符来传递参数时，表示参数在方法内是只读的，方法内部不能修改该参数的值
+ 当传递大型结构体或类对象时，使用 in 修饰符可以避免不必要的拷贝操作，提高性能

```
void MyMethod(in int x)
{
    // 在这里不能修改 x 的值
}

int value = 42;
MyMethod(in value);
```
#### in 作为 泛型参数类型

+ in 作为泛型参数类型的一部分通常用于接口和委托，可指定类型参数是逆变的
+ 在泛型接口和委托中使用 in 关键字,使用更泛化的类型替代更具体的类型
+ 具有 逆变类型参数 的接口 使其 方法接受的参数的类型 可以比 接口类型参数 指定的类型 派生程度 更小

```
// 1. 逆变泛型接口
// 逆变 接口 
interface IContravariant<in A> { }

// 扩展 逆变 接口 
interface IExtContravariant<in A> : IContravariant<A> { }

// 实现 逆变 接口
class Sample<A> : IContravariant<A> { }

class Program
{
    static void Test()
    {
        IContravariant<Object> iobj = new Sample<Object>();
        IContravariant<String> istr = new Sample<String>();

        // 可以把 iobj 赋值 给 istr
        // 因为 IContravariant 是逆变接口，类型可以逆变
        istr = iobj;
    }
}

// 2. 逆变泛型委托
// 逆变 委托
public delegate void DContravariant<in A>(A argument);

//  匹配 委托签名 的方法
public static void SampleControl(Control control){ }
public static void SampleButton(Button button){ }

public void Test()
{

    // 使用方法实例化委托
    DContravariant<Control> dControl = SampleControl;
    DContravariant<Button> dButton = SampleButton;

    // 可以把 dControl 赋值 给 dButton
    // 因为 DContravariant 是逆变委托，类型可以逆变
    dButton = dControl;

    // 调用委托
    dButton(new Button());
}
```

**两种用法之间的关键区别在于上下文：一个是方法参数的上下文，一个是泛型接口或委托的上下文**

in: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/in-generic-modifier

###  2. out

#### out 作为修饰符

+ out 修饰符 用于指示参数是一个输出参数，允许方法修改参数的值并将其传递回调用方
+ 调用方在调用方法之前不需要为 out 参数分配值
+ 方法内部必须在退出之前为 out 参数分配一个值

```
void MyMethod(out int result)
{
    // 在这里必须为 result 分配一个值
    result = 42;
}

// 调用 MyMethod 时，不需要初始化 result，因为它是 out 参数
int outputValue;
MyMethod(out outputValue);
// 此时 outputValue 的值为 42
```

#### out  作为 泛型参数类型

+ out 作为泛型参数类型的一部分通常用于接口和委托，可指定类型参数是协变的
+ 在泛型接口和委托中使用 out 关键字,可以  使得泛型参数类型可以被替代为其派生类型
+ 具有协变类型参数的接口使其方法返回的类型可以比类型参数指定的类型派生程度更大

```
// 1. 协变泛型接口
// 协变 接口
interface ICovariant<out R> { }

// 扩展 协变 接口
interface IExtCovariant<out R> : ICovariant<R> { }

// 实现 协变 接口
class Sample<R> : ICovariant<R> { }

class Program
{
    static void Test()
    {
        ICovariant<Object> iobj = new Sample<Object>();
        ICovariant<String> istr = new Sample<String>();

        // 可以将 istr  赋值给 iobj
        // Covariant 接口是 协变
        iobj = istr;
    }
}

// 2. 协变泛型 委托
// 协变 委托
public delegate R DCovariant<out R>();

// 匹配 委托签名 的方法
public static Control SampleControl()
{ 
    return new Control(); 
}

public static Button SampleButton()
{
     return new Button(); 
}

public void Test()
{
    // 使用方法 实例化 委托
    DCovariant<Control> dControl = SampleControl;
    DCovariant<Button> dButton = SampleButton;

    // 把 dButton 赋值给  dControl
    // DCovariant委托是协变的
    dControl = dButton;

    // 调用委托
    dControl();
}
```

**两种用法之间的关键区别在于上下文：一个是方法参数的上下文，一个是泛型接口或委托的上下文**

out : https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/out-generic-modifier

#### 逆变和协变

协变（Covariance）和逆变（Contravariance）是泛型类型参数在继承关系中的两个重要概念，它们指定了在派生类型和基类型之间如何处理泛型类型参数
+ 协变允许泛型类型参数在派生类型中使用更为具体的类型
+ 逆变允许泛型类型参数在派生类型中使用更为抽象的类型
+ 协变使用 out 关键字，逆变使用 in 关键字


##### 1. 逆变（Contravariance）

+ 表示泛型类型参数能够在派生类型中使用更为抽象的类型（基类型）
+ 在 C# 中，逆变使用 in 关键字来标记泛型参数，同样通常出现在接口和委托中
+ 逆变允许将一个泛型类型参数更为抽象的类型传递给一个期望派生类型的情况

```
// 示例中，T 是逆变的
delegate void MyDelegate<in T>(T item);

// 可以将 Action<Base> 赋值给 MyDelegate<Derived>
MyDelegate<Derived> myAction = new Action<Base>(someBase => { /* 实现 */ });
```

##### 2. 协变（Covariance）
+ 协变表示泛型类型参数能够在派生类型中使用更为具体的类型（派生类型）
+ 在 C# 中，协变使用 out 关键字来标记泛型参数，通常出现在接口和委托中
+ 协变允许将一个泛型类型参数更为具体的类型传递给一个期望基类型的情况

```
// 示例中，T 是协变的
interface IMyInterface<out T>
{
    T GetItem();
}

// 可以将 MyImplementation<Derived> 赋值给 IMyInterface<Base>
IMyInterface<Base> myObj = new MyImplementation<Derived>();
```

逆变和协变：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/

###  3. ref

+ ref 关键字用于将参数传递作为引用
+ 通过 ref，方法可以修改调用方提供的变量的值
+ 调用方必须初始化变量，且在调用方法时，参数需要使用 ref 关键字

```
void ModifyValue(ref int x)
{
    x = x * 2;
}

int value = 5;
ModifyValue(ref value);
// 此时 value 的值为 10
```

ref: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/ref

###  4. params

+ params 关键字用于表示一个可变数量的参数
+ 该参数必须是数组类型，而调用方可以提供不同数量的参数
+ 在方法声明中的 params 关键字之后不允许有任何其他参数，并且在方法声明中只允许有一个 params 关键字
+ 使用 params 参数调用方法时，可以传入：
  + 数组元素类型的参数的逗号分隔列表
  + 指定类型的参数的数组
  + 无参数。 如果未发送任何参数，则 params 列表的长度为零

```
int Sum(params int[] numbers)
{
    int sum = 0;
    foreach (int num in numbers)
    {
        sum += num;
    }
    return sum;
}

int total = Sum(1, 2, 3, 4, 5);
// 此时 total 的值为 15
```

ps
关于参数修饰符具体可参考： https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/method-parameters#params-modifier

备注： 关于其他修饰符如 `abstract`、`const`、`event`、`extern`、`override`、`sealed`、`static`、`virtual`、`volatile`、`unsafe`等修饰符将放在第二篇
