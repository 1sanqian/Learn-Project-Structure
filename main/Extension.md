# 一、Extension扩展

扩展方法（Extension Methods）允许开发人员向现有的类添加新的方法，而无需创建新的派生类型、修改类的源代码或继承该类、重新编译或以其他方式修改原始类型

扩展方法提供了一种在不修改现有类的情况下，为该类添加新功能的方式，是一种静态方法，但可以像扩展类型上的实例方法一样进行调用

#### 1. 扩展方法的特点

##### 定义方式

扩展方法是在静态类中定义的静态方法，必须定义在一个静态类中。静态类必须是非嵌套的，并且命名空间中的所有代码文件都可以使用扩展方法

##### 扩展方法的声明

扩展方法的第一个参数是要扩展的类型，使用 this 修饰符指定该参数，使用this关键字 后面跟着要操作的对象类型，这个参数表示要在其上调用扩展方法的实例

```
public static void PrintStrLength(this string str)
```

##### 使用方式

使用扩展方法时，可以像调用实例方法一样使用点运算符，将扩展方法应用于相应的对象

```
// 定义扩展方法的静态类
public static class StringExtensions
{
    // 扩展方法Reverse：将字符串反转
    public static string Reverse(this string str) // 接受一个字符串作为参数，并将该字符串反转
    {
        char[] charArray = str.ToCharArray();
        Array.Reverse(charArray);
        return new string(charArray);
    }
}

// 使用扩展方法
string originalString = "Hello";
string reversedString = originalString.Reverse();
Console.WriteLine(reversedString);  // 输出：olleH
```

通过扩展方法，可以在不修改原始类的情况下，为现有类型添加新的功能和行为，可以方便地扩展和定制现有的类库或框架

**注意**： 如果要扩展的类中本来就有和扩展方法的名称一样的方法，编译器默认认为一个表达式是要使用一个实例方法，但如果没有找到，就会检查导入的命名空间和当前命名空间里所有的扩展方法，并匹配到适合的方法。在导入扩展类时，仅当使用 using 指令将命名空间显式导入到源代码中之后，扩展方法才位于范围中。


##### 示例
演示 C# 编译器在确定是将方法调用绑定到类型上的实例方法还是绑定到扩展方法时所遵循的规则

```
// Define an interface named IMyInterface.
namespace DefineIMyInterface
{
    public interface IMyInterface
    {
        // Any class that implements IMyInterface must define a method
        // that matches the following signature.
        void MethodB();
    }
}

// Define extension methods for IMyInterface.
namespace Extensions
{
    using System;
    using DefineIMyInterface;

    // The following extension methods can be accessed by instances of any
    // class that implements IMyInterface.
    public static class Extension
    {
        public static void MethodA(this IMyInterface myInterface, int i)
        {
            Console.WriteLine
                ("Extension.MethodA(this IMyInterface myInterface, int i)");
        }

        public static void MethodA(this IMyInterface myInterface, string s)
        {
            Console.WriteLine
                ("Extension.MethodA(this IMyInterface myInterface, string s)");
        }

        // This method is never called in ExtensionMethodsDemo1, because each
        // of the three classes A, B, and C implements a method named MethodB
        // that has a matching signature.
        public static void MethodB(this IMyInterface myInterface)
        {
            Console.WriteLine
                ("Extension.MethodB(this IMyInterface myInterface)");
        }
    }
}

// Define three classes that implement IMyInterface, and then use them to test
// the extension methods.
namespace ExtensionMethodsDemo1
{
    using System;
    using Extensions;
    using DefineIMyInterface;

    class A : IMyInterface
    {
        public void MethodB() { Console.WriteLine("A.MethodB()"); }
    }

    class B : IMyInterface
    {
        public void MethodB() { Console.WriteLine("B.MethodB()"); }
        public void MethodA(int i) { Console.WriteLine("B.MethodA(int i)"); }
    }

    class C : IMyInterface
    {
        public void MethodB() { Console.WriteLine("C.MethodB()"); }
        public void MethodA(object obj)
        {
            Console.WriteLine("C.MethodA(object obj)");
        }
    }

    class ExtMethodDemo
    {
        static void Main(string[] args)
        {
            // Declare an instance of class A, class B, and class C.
            A a = new A();
            B b = new B();
            C c = new C();

            // For a, b, and c, call the following methods:
            //      -- MethodA with an int argument
            //      -- MethodA with a string argument
            //      -- MethodB with no argument.

            // A contains no MethodA, so each call to MethodA resolves to
            // the extension method that has a matching signature.
            a.MethodA(1);           // Extension.MethodA(IMyInterface, int)
            a.MethodA("hello");     // Extension.MethodA(IMyInterface, string)

            // A has a method that matches the signature of the following call
            // to MethodB.
            a.MethodB();            // A.MethodB()

            // B has methods that match the signatures of the following
            // method calls.
            b.MethodA(1);           // B.MethodA(int)
            b.MethodB();            // B.MethodB()

            // B has no matching method for the following call, but
            // class Extension does.
            b.MethodA("hello");     // Extension.MethodA(IMyInterface, string)

            // C contains an instance method that matches each of the following
            // method calls.
            c.MethodA(1);           // C.MethodA(object)
            c.MethodA("hello");     // C.MethodA(object)
            c.MethodB();            // C.MethodB()
        }
    }
}
/* Output:
    Extension.MethodA(this IMyInterface myInterface, int i)
    Extension.MethodA(this IMyInterface myInterface, string s)
    A.MethodB()
    B.MethodA(int i)
    B.MethodB()
    Extension.MethodA(this IMyInterface myInterface, string s)
    C.MethodA(object obj)
    C.MethodA(object obj)
    C.MethodB()
 */
```

#### 2. 扩展预定义类型

当需要创建可重用功能时，我们无需创建新对象，而是可以扩展现有类型

使用 `struct`  类型扩展预定义类型可能很困难，因为它们已通过值传递给方法。 这意味着将对结构的副本进行任何结构更改。 扩展方法退出后，将不显示这些更改

1. 可以将 `ref` 修饰符添加到第一个参数，使其成为 ref 扩展方法。 ref  关键字可以在 this 关键字之前或之后显示，不会有任何语义差异。 添加  ref  修饰符表示第一个参数是按引用传递的

2. 在这种情况下，可以编写扩展方法来更改要扩展的结构的状态（请注意，私有成员不可访问）
仅允许值类型或受结构约束的泛型类型（有关详细信息，请参阅 [struct 约束](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/struct#struct-constraint)）作为 ref 扩展方法的第一个参数

以下示例是如何使用  ref 扩展方法直接修改内置类型，而无需重新分配结果或使用 ref  关键字传递函数：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-04294a928d0fda95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以下示例是用户自定义的结构类型的 ref 扩展方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-a330e78cc8cbec11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3. 扩展方法的问题

使用扩展方法明显比使用传统方法要简单，不需要实例化，修改方法也不需要重新修改和编译，但也还是有一些问题：

1）扩展方法不能访问被扩展类的私有成员

2）扩展方法会被扩展类的同名方法覆盖，所以实现扩展方法我们需要承担随时被覆盖的风险

3）只有引入扩展方法所在的命名空间后，扩展方法才可以使用

4）扩展方法不具有override的能力，不会重载原有的方法

**注意**：将 实例方法调用 在编译期 改变为 静态类中的静态方法调用，实际上扩展方法确实拥有静态方法所具有的所有功能

#### 4.通用准则

在使用扩展方法来扩展你无法控制其源代码的类型时，需要承受该类型实现中的更改会导致扩展方法失效的风险

如果确实为给定类型实现了扩展方法，请记住以下几点：

+ 如果扩展方法与该类型中定义的方法具有相同的签名，则扩展方法永远不会被调用
+ 在命名空间级别将扩展方法置于范围中。 例如，如果你在一个名为 Extensions 的命名空间中具有多个包含扩展方法的静态类，则这些扩展方法将全部由 using Extensions; 指令置于范围中

最常见的扩展方法是 LINQ 标准查询运算符，它将查询功能添加到现有的 [System.Collections.IEnumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerable) 和 [System.Collections.Generic.IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 类型。 若要使用标准查询运算符，请先使用 `using System.Linq` 指令将它们置于范围中。 然后，任何实现了 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 的类型看起来都具有 [GroupBy](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.groupby)、[OrderBy](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.orderby)、[Average](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.average) 等实例方法

ps:

Extension : https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/extension-methods

System.Xml.Linq 的 Extensions Class : https://learn.microsoft.com/en-us/dotnet/api/system.xml.linq.extensions?view=net-8.0

