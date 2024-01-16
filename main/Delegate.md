# 一、Delegate 委托

委托（Delegate） 是一种引用类型，表示对具体特定参数列表和返回类型的方法的引用，在实例化委托时，可以将其实例与任何具有兼容签名和返回类型的方法的相关联，可以通过委托实例调用方法。

委托（Delegate）特别用于实现事件和回调方法。所有的委托（Delegate）都派生自 System.Delegate 类

委托用于将方法作为参数传递给其他方法。事件处理程序就是通过委托调用的方法

委托具有以下的属性：
+ 委托类似于 C++ 函数指针，但委托完全面向对象，不像 C++ 指针会记住函数，委托会同时封装对象实例和方法
+ 委托允许将方法作为参数进行传递
+ 委托可用于定义回调方法
+ 委托可以链接在一起；例如，可以对一个事件调用多个方法
+ 方法不必与委托类型完全匹配。

#### 1. 委托声明

可将任何可访问类或结构中与委托类型相匹配的任何方法分配给委托，该方法可以是静态方法，也可以是实例方法

+ 使用匹配签名声明委托类型并声明方法

```
// Declare a delegate.
delegate void NotifyCallback(string str);

// Declare a method with the same signature as the delegate.
static void Notify(string name)
{
    Console.WriteLine($"Notification received for: {name}");
}

// Create an instance of the delegate.
NotifyCallback del1 = new NotifyCallback(Notify);
```

+ 将方法组分配给委托类型

```
// C# 2.0 provides a simpler way to declare an instance of NotifyCallback.
NotifyCallback del2 = Notify;
```

+ 声明匿名方法

```
// Instantiate NotifyCallback by using an anonymous method.
NotifyCallback del3 = delegate(string name)
{ 
    Console.WriteLine($"Notification received for: {name}"); 
};
```

+ 使用 lambda 表达式

```
 // Instantiate NotifyCallback by using a lambda expression.
NotifyCallback del4 = name =>  { Console.WriteLine($"Notification received for: {name}"); };
```

#### 2. 委托声明，实例化以及使用的例子

![image.png](https://upload-images.jianshu.io/upload_images/29476859-91e6ab0d2c92a1d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ 声明委托

```
public delegate void MyDelegate(string message);

//委托声明的格式一般如下
 delegate returnType DelegateName(parameterList);
```

+ 实例化委托
     + 在实例化委托之前，先创建一个与委托签名相匹配的方法

       ```
       public void MyMethod(string message)
       {
            Console.WriteLine(message);
        }
       ```

     + 实例化委托

       ```
       MyDelegate  delegateInstance = new MyDelegate(MyMethod);
       ```

+ 调用委托

```
delegateInstance("Hello World");
```

**备注：在方法重载的上下文中，方法的签名 不包括 返回值；但在委托的上下文中，签名 包括 返回值；即方法和委托必须具有相同的返回类型**

# 二、Func和Action

Func 和 Action 是两种常见的委托类型

#### 1. Func

+ Func是一个泛型委托类型
+ 可以引用具有指定返回类型的方法
+ 可以接受零个或者多个输入参数，并返回一个值
+ 最后一个泛型参数表示返回类型

#### 2. Action 

+ Action 是一个泛型委托类型
+ 可以引用不返回值的方法
+ 可以接受零个或者多个输入参数， 但不返回任何值

示例

```
// Func
 public static void Main(string[] args)
    {
        // Func 委托，接受两个 int 类型参数并返回一个 int 类型的值
        Func<int, int, int> add = (x, y) => x + y;

        // 调用 Func 委托
        int result = add(5, 3);
        Console.WriteLine("Result of addition: " + result);
    }

// Action
 public static void Main(string[] args)
    {
        // Action 委托，接受两个 int 类型参数并不返回任何值（void）
        Action<int, int> displaySum = (x, y) =>
        {
            int sum = x + y;
            Console.WriteLine("Sum: " + sum);
        };

        // 调用 Action 委托
        displaySum(5, 3);
    }
```


# 三、带有命名方法的委托与匿名方法

委托可以与命名方法相关联。 使用命名方法实例化委托时，该方法作为参数传递，例如：

```
// Declare a delegate.
delegate void WorkCallback(int x);

// Define a named method.
void DoWork(int k) { /* ... */ }

//  这种方式称为使用命名方法
// Instantiate the delegate using the method as a parameter.
WorkCallback d = obj.DoWork;   // 并没有去new 一个委托传递方法，而是直接作为参数传递
```

使用命名方法构造的委托可以封装静态方法或实例方法

作为委托参数传递的方法必须具有与委托声明相同的签名

以下是 使用命名方法 的一个简单示例

```
// Declare a delegate
delegate void MultiplyCallback(int i, double j);

class MathClass
{
    static void Main()
    {
        MathClass m = new MathClass();

        // Delegate instantiation using "MultiplyNumbers"
        MultiplyCallback d = m.MultiplyNumbers;

        // Invoke the delegate object.
        Console.WriteLine("Invoking the delegate using 'MultiplyNumbers':");
        for (int i = 1; i <= 5; i++)
        {
            d(i, 2);
        }

        // Keep the console window open in debug mode.
        Console.WriteLine("Press any key to exit.");
        Console.ReadKey();
    }

    // Declare the associated method.
    void MultiplyNumbers(int m, double n)
    {
        Console.Write(m * n + " ");
    }
}
/* Output:
    Invoking the delegate using 'MultiplyNumbers':
    2 4 6 8 10
*/
```

在下面的示例中，一个委托映射到静态方法和实例方法，并返回来自两种方法的具体信息

```
// Declare a delegate
delegate void Callback();

class SampleClass
{
    public void InstanceMethod()
    {
        Console.WriteLine("A message from the instance method.");
    }

    static public void StaticMethod()
    {
        Console.WriteLine("A message from the static method.");
    }
}

class TestSampleClass
{
    static void Main()
    {
        var sc = new SampleClass();

        // Map the delegate to the instance method:
        Callback d = sc.InstanceMethod;
        d();

        // Map to the static method:
        d = SampleClass.StaticMethod;
        d();
    }
}
/* Output:
    A message from the instance method.
    A message from the static method.
*/
```

**备注：尽管委托可以使用 [out](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/method-parameters#out-parameter-modifier) 参数，但不建议将该委托与多播事件委托配合使用，因为你无法知道将调用哪个委托**

# 四、合并委托（多播委托）
 
委托对象的一个有用属性 在于 可通过使用

+ `+` 运算符 将 多个对象 分配到一个 委托实例

+ `-` 运算符可用于从多播委托中删除组件委托

+ 多播委托 包含 已分配委托列表 

+ 仅可合并类型相同的委托

以下示例是创建多播委托，此多播委托被调用时会依次调用列表中的委托

```
using System;

// Define a custom delegate that has a string parameter and returns void.
delegate void CustomCallback(string s);

class TestClass
{
    // Define two methods that have the same signature as CustomCallback.
    static void Hello(string s)
    {
        Console.WriteLine($"  Hello, {s}!");
    }

    static void Goodbye(string s)
    {
        Console.WriteLine($"  Goodbye, {s}!");
    }

    static void Main()
    {
        // Declare instances of the custom delegate.
        CustomCallback hiDel, byeDel, multiDel, multiMinusHiDel;

        // In this example, you can omit the custom delegate if you
        // want to and use Action<string> instead.
        //Action<string> hiDel, byeDel, multiDel, multiMinusHiDel;

        // Initialize the delegate object hiDel that references the
        // method Hello.
        hiDel = Hello;

        // Initialize the delegate object byeDel that references the
        // method Goodbye.
        byeDel = Goodbye;

        // The two delegates, hiDel and byeDel, are combined to
        // form multiDel.
        multiDel = hiDel + byeDel;

        // Remove hiDel from the multicast delegate, leaving byeDel,
        // which calls only the method Goodbye.
        multiMinusHiDel = multiDel - hiDel;

        Console.WriteLine("Invoking delegate hiDel:");
        hiDel("A");
        Console.WriteLine("Invoking delegate byeDel:");
        byeDel("B");
        Console.WriteLine("Invoking delegate multiDel:");
        multiDel("C");
        Console.WriteLine("Invoking delegate multiMinusHiDel:");
        multiMinusHiDel("D");
    }
}
/* Output:
Invoking delegate hiDel:
  Hello, A!
Invoking delegate byeDel:
  Goodbye, B!
Invoking delegate multiDel:
  Hello, C!
  Goodbye, C!
Invoking delegate multiMinusHiDel:
  Goodbye, D!
*/
```

ps：

Delegate 委托：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/delegates/
