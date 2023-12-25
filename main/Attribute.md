# 一、Attribute —— 特性

特性（Attribute）是用于在运行时传递程序中各种元素（比如类、方法、结构、枚举、组件等）的行为信息的声明性标签，可以通过使用特性向程序添加声明性信息，一个声明性标签是通过放置在它所应用的元素前面的方括号（[ ]）来描述的

特性（Attribute）用于添加元数据，如编译器指令和注释、描述、方法、类等其他信息。.Net 框架提供了两种类型的特性：预定义特性和自定义特性

+ 特性（Attribute）的语法如下：

```
[attribute(positional_parameters, name_parameter = value, ...)]
element
```

特性（Attribute）的名称和值是在方括号内规定的，放置在它所应用的元素之前
- positional_parameters 规定必需的信息
- name_parameter 规定可选的信息

### 1. 预定义特性

(1) AttributeUsage

规定该特性的语法如下：

```
[AttributeUsage(validon，AllowMultiple = allowmultiple，Inherited = inherited)]

// 用例
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Constructor | AttributeTargets.Field | AttributeTargets.Method | AttributeTargets.Property,  AllowMultiple = true)]

// AttributeTargets.Class  应用于类
// AttributeTargets.Constructor 应用于构造函数
// AttributeTargets.Field 应用于字段
// AttributeTargets.Method 应用于方法
// AttributeTargets.Property 应用于属性
```

AttributeUsage: 描述了如何使用一个自定义特性类，它规定了特性可应用到的项目的类型
- validon 规定特性可被放置的语言元素，是枚举器AttributeTargets的值的组合，默认值是 AttributeTargets.All
- allowmultiple（可选的）为该特性的 AllowMultiple 属性（property）提供一个布尔值。如果为 true，则该特性是多用的，默认值是 false（单用的）
- inherited（可选的）为该特性的 Inherited 属性（property）提供一个布尔值。如果为 true，则该特性可被派生类继承，默认值是 false（不被继承）

(2) Conditional

规定该特性的语法如下：

```
[Conditional(conditionalSymbol)]

// 用例
#define DEBUG
using System;
using System.Diagnostics;
public class Myclass
{
    [Conditional("DEBUG")]
    public static void Message(string msg)
    {
        Console.WriteLine(msg);
    }
}
class Test
{
    static void function1()
    {
        Myclass.Message("In Function 1.");
        function2();
    }
    static void function2()
    {
        Myclass.Message("In Function 2.");
    }
    public static void Main()
    {
        Myclass.Message("In Main function.");
        function1();
        Console.ReadKey();
    }
}

//  [Conditional("DEBUG")] 运行时的结果是 ：
/*
In Main function.
In Function 1.
In Function 2.
*/

// 若改为[Conditional("TRACE")]运行时的结果是 ：
/*
In Main function.
*/

// 若改为#define TRACE  [Conditional("TRACE")]运行时的结果是 ：
/*
In Main function.
In Function 1.
In Function 2.
*/

//"TRACE" 是一个条件符号，它表示只有在代码中定义了 "TRACE" 符号时，才会编译并执行带有该属性的方法。
```

Conditional: 标记了一个条件方法，其执行依赖于指定的预处理标识符，Conditional会引起方法调用的条件编译，取决于指定的值，比如 Debug 或 Trace。例如，当调试代码时显示变量的值

(3) Obsolete

规定该特性的语法如下：

```
[Obsolete(message)]
[Obsolete(message, iserror)]

//用例
using System;
public class MyClass
{
   [Obsolete("Don't use OldMethod, use NewMethod instead", true)]
   static void OldMethod()
   {
      Console.WriteLine("It is the old method");
   }
   static void NewMethod()
   {
      Console.WriteLine("It is the new method");
   }
   public static void Main()
   {
      OldMethod();
   }
}

// 运行时，编译器会给出一个错误的信息说明：
Don't use OldMethod, use NewMethod instead
```

Obsolete标记了不应被使用的程序实体。它可以让您通知编译器丢弃某个特定的目标元素。例如，当一个新方法被用在一个类中，但是仍然想要保持类中的旧方法，可以通过显示一个应该使用新方法，而不是旧方法的消息，来把它标记为 obsolete（过时的）
- 参数 message，是一个字符串，描述项目为什么过时以及该替代使用什么
- 参数 iserror，是一个布尔值。如果该值为 true，编译器应把该项目的使用当作一个错误。默认值是 false（编译器生成一个警告）

### 2. 自定义特性

.Net 框架允许创建自定义特性，用于存储声明性的信息，且可在运行时被检索，以通过定义属性类来创建自己的自定义属性，属性类是直接或间接派生自[Attribute](https://learn.microsoft.com/en-us/dotnet/api/system.attribute) 的类，这使得识别元数据中的属性定义变得快速而轻松

建并使用自定义特性包含四个步骤：

+ 声明自定义特性
+ 构建自定义特性
+ 在目标程序元素上应用自定义特性
+ 通过反射访问特性（包含编写一个简单的程序来读取元数据以便查找各种符号，元数据是用于描述其他数据的数据和信息。该程序应使用反射来在运行时访问特性）

```
// 1. 声明了一个名为 DeBugInfo 的自定义特性，一个新的自定义特性应派生自 System.Attribute 类
// 一个自定义特性 BugFix 被赋给类及其成员
[AttributeUsage(AttributeTargets.Class |
AttributeTargets.Constructor |
AttributeTargets.Field |
AttributeTargets.Method |
AttributeTargets.Property,
AllowMultiple = true)]

public class DeBugInfo : System.Attribute
{
// 2.构建一个DeBugInfo 的自定义特性，DeBugInfo将存储 调试程序 获得的信息
  private int bugNo;  // 私有的变量： bug 的代码编号
  private string developer;  // 私有的变量： 辨认该 bug 的开发人员名字
  private string lastReview;  //私有的变量 ：最后一次审查该代码的日期
  public string message;  // 公有变量：一个存储了开发人员标记的字符串消息

// 必需的[bugNo,developer,lastReview]参数应通过构造函数传递。
  public DeBugInfo(int bg, string dev, string d)
  {
      this.bugNo = bg;
      this.developer = dev;
      this.lastReview = d;
  }

// 每个数据的get,set方法，必要参数的set 方法已通过构造函数设置
  public int BugNo
  {
      get
      {
          return bugNo;
      }
  }
  public string Developer
  {
      get
      {
          return developer;
      }
  }
  public string LastReview
  {
      get
      {
          return lastReview;
      }
  }
  public string Message
  {
      get
      {
          return message;
      }
      set
      {
          message = value;
      }
  }
}

// 3.应用自定义特性，通过把特性放置在紧接着它的目标之前，来应用该特性
[DeBugInfo(45, "Zara Ali", "12/8/2012", Message = "Return type mismatch")]
[DeBugInfo(49, "Nuha Ali", "10/10/2012", Message = "Unused variable")]
class Rectangle
{
  // 成员变量
  protected double length;
  protected double width;
  public Rectangle(double l, double w)
  {
      length = l;
      width = w;
  }
  [DeBugInfo(55, "Zara Ali", "19/10/2012",
  Message = "Return type mismatch")]
  public double GetArea()
  {
      return length * width;
  }
  [DeBugInfo(56, "Zara Ali", "19/10/2012")]
  public void Display()
  {
      Console.WriteLine("Length: {0}", length);
      Console.WriteLine("Width: {0}", width);
      Console.WriteLine("Area: {0}", GetArea());
  }
}  //end class Rectangle 

class ExecuteRectangle
   {
      static void Main(string[] args)
      {
         Rectangle r = new Rectangle(4.5, 7.5);
         r.Display();
         Type type = typeof(Rectangle);
         // 遍历 Rectangle 类的特性
         foreach (Object attributes in type.GetCustomAttributes(false))   // type.GetCustomAttributes(false) 是一个反射方法，用于获取指定类型的自定义属性
         {
            DeBugInfo dbi = (DeBugInfo)attributes;
            if (null != dbi)
            {
               Console.WriteLine("Bug no: {0}", dbi.BugNo);
               Console.WriteLine("Developer: {0}", dbi.Developer);
               Console.WriteLine("Last Reviewed: {0}", dbi.LastReview);
               Console.WriteLine("Remarks: {0}", dbi.Message);
            }
         }
         
         // 遍历Rectangle 类方法特性
         foreach (MethodInfo m in type.GetMethods())
         {
            foreach (Attribute a in m.GetCustomAttributes(true))
            {
               DeBugInfo dbi = (DeBugInfo)a;
               if (null != dbi)
               {
                  Console.WriteLine("Bug no: {0}, for Method: {1}", dbi.BugNo, m.Name);
                  Console.WriteLine("Developer: {0}", dbi.Developer);
                  Console.WriteLine("Last Reviewed: {0}", dbi.LastReview);
                  Console.WriteLine("Remarks: {0}", dbi.Message);
               }
            }
         }
         Console.ReadLine();
      }
   } 
/* 运行结果
Length: 4.5
Width: 7.5
Area: 33.75
Bug No: 49
Developer: Nuha Ali
Last Reviewed: 10/10/2012
Remarks: Unused variable
Bug No: 45
Developer: Zara Ali
Last Reviewed: 12/8/2012
Remarks: Return type mismatch
Bug No: 55, for Method: GetArea
Developer: Zara Ali
Last Reviewed: 19/10/2012
Remarks: Return type mismatch
Bug No: 56, for Method: Display
Developer: Zara Ali
Last Reviewed: 19/10/2012
Remarks: 
*/
```

ps: 
关于 Attribute 具体内容可以查看官网： https://learn.microsoft.com/en-us/dotnet/api/system.attribute?view=net-8.0
