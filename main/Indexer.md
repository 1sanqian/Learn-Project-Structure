# 一、Indexer索引器

索引器允许类或结构的实例就像数组一样进行索引， 无需显式指定类型或实例成员，即可 设置或检索 索引值。 索引器类似于[属性](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/properties)，不同之处在于它们的访问器需要使用参数

#### 1. 概述

*   使用索引器可以用类似于数组的方式为对象建立索引

*   `get` 取值函数返回值。 `set` 取值函数分配值

*   [this](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/this) 关键字用于定义索引器

*   [value](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/value) 关键字用于定义由 `set` 访问器分配的值

*   索引器不必根据整数值进行索引；由你决定如何定义特定的查找机制

*   索引器可被重载

*   索引器可以有多个形参，例如当访问二维数组时

索引器语法：可以通过变量名和方括号访问索引器。 将索引器参数放在方括号内：

```
var item = someObject["key"];
someObject["AnotherKey"] = item;

// 使用 this 关键字作为属性名声明索引器，并在方括号内声明参数
public int this[string key]
{
    get { return storage.Find(key); }
    set { storage.SetAt(key, value); }
}
```


![image.png](https://upload-images.jianshu.io/upload_images/29476859-d01fbc751c7ae955.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自 C# 7.0 起，get 和 set 访问器均可作为表达式主体成员实现。 在这种情况下，必须使用 get 和 set 关键字

```
using System;

class SampleCollection<T>
{
   // Declare an array to store the data elements.
   private T[] arr = new T[100];

   // Define the indexer to allow client code to use [] notation.
   public T this[int i]
   {
      get => arr[i];
      set => arr[i] = value;
   }
}

class Program
{
   static void Main()
   {
      var stringCollection = new SampleCollection<string>();
      stringCollection[0] = "Hello, World.";
      Console.WriteLine(stringCollection[0]);
   }
}
// The example displays the following output:
//       Hello, World.
```

#### 2. 使用索引器

索引器使你可从语法上方便地创建类、结构或接口，以便客户端应用程序可以像访问数组一样访问它们。 编译器将生成一个 `Item` 属性（或者如果存在 [IndexerNameAttribute](https://learn.microsoft.com/zh-cn/dotnet/api/system.runtime.compilerservices.indexernameattribute)，也可以生成一个命名属性）和适当的访问器方法

若要在类或结构上声明索引器，请使用 [this](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/this) 关键字

```
// Indexer declaration
public int this[int index]
{
    // get and set accessors
}
```

**重要:通过声明索引器，可自动在对象上生成一个名为 Item 的属性。 无法从实例成员访问表达式直接访问 `Item` 属性。 此外，如果通过索引器向对象添加自己的 Item 属性，则将收到 [CS0102 编译器错误](https://learn.microsoft.com/zh-cn/dotnet/csharp/misc/cs0102)。 要避免此错误，请使用 IndexerNameAttribute 来 重命名 索引器**

+ 索引器及其参数的类型必须至少具有和索引器相同的可访问性
+ 索引器的签名由其形参的数目和类型所组成，签名不包含索引器类型或形参的名称
+ 如果要在相同类中声明多个索引器，则它们的签名必须不同
+ 索引器未分类为变量，所以索引器值不能按引用（作为 ref 或  out 参数）传递，除非其值是引用（即按引用返回）
+ 若要使 索引器的名称 可为其他语言所用，请使用 [System.Runtime.CompilerServices.IndexerNameAttribute](https://learn.microsoft.com/zh-cn/dotnet/api/system.runtime.compilerservices.indexernameattribute)

```
// Indexer declaration
//索引器被索引器名称属性重写，因此其名称将为 TheItem。 默认情况下，默认名称为 Item
[System.Runtime.CompilerServices.IndexerName("TheItem")]
public int this[int index]
{
    // get and set accessors
}
```

示例

```
using Day = System.DayOfWeek;

class DayOfWeekCollection
{
    Day[] days =
    [
        Day.Sunday, Day.Monday, Day.Tuesday, Day.Wednesday,
        Day.Thursday, Day.Friday, Day.Saturday
    ];

    // Indexer with only a get accessor with the expression-bodied definition:
    public int this[Day day] => FindDayIndex(day);

    private int FindDayIndex(Day day)
    {
        for (int j = 0; j < days.Length; j++)
        {
            if (days[j] == day)
            {
                return j;
            }
        }
        throw new ArgumentOutOfRangeException(
            nameof(day),
            $"Day {day} is not supported.\nDay input must be a defined System.DayOfWeek value.");
    }
}
// 使用索引器
var week = new DayOfWeekCollection();
Console.WriteLine(week[DayOfWeek.Friday]);

try
{
    Console.WriteLine(week[(DayOfWeek)43]);
}
catch (ArgumentOutOfRangeException e)
{
    Console.WriteLine($"Not supported input: {e.Message}");
}
```

#### 4. 接口中的索引器

可以在 接口 上声明索引器。 接口索引器的访问器与 类 索引器的访问器有所不同，差异如下：

*   接口访问器不使用修饰符
*   接口访问器通常没有正文

访问器的用途是指示索引器为读写、只读还是只写，可以为接口中定义的索引器提供实现，但这种情况非常少，索引器通常定义 API 来访问数据字段，而数据字段无法在接口中定义

```
// 索引器的签名必须不同于同一接口中声明的所有其他索引器的签名
// Indexer on an interface:
public interface IIndexInterface
{
    // Indexer declaration:
    int this[int index]
    {
        get;
        set;
    }
}

// 继承接口索引器
// Implementing the interface.
class IndexerClass : IIndexInterface
{
    private int[] arr = new int[100];
    public int this[int index]   // indexer declaration
    {
        // The arr object will throw IndexOutOfRange exception.
        get => arr[index];
        set => arr[index] = value;
    }
}

// 使用 接口 索引器
IndexerClass test = new IndexerClass();
//可共享的全局随机数生成器。这意味着多个线程或多个部分可以使用同一个全局随机数生成器，而无需每次都创建新的实例
System.Random rand = System.Random.Shared;
// Call the indexer to initialize its elements.
for (int i = 0; i < 10; i++)
{
    test[i] = rand.Next();
}
for (int i = 0; i < 10; i++)
{
    System.Console.WriteLine($"Element #{i} = {test[i]}");
}

/* Sample output:
    Element #0 = 360877544
    Element #1 = 327058047
    Element #2 = 1913480832
    Element #3 = 1519039937
    Element #4 = 601472233
    Element #5 = 323352310
    Element #6 = 1422639981
    Element #7 = 1797892494
    Element #8 = 875761049
    Element #9 = 393083
```

仅当类采用相同的索引签名实现多个接口时，可通过使用接口成员的完全限定名来使用显示接口成员实现，以避免歧义，例： 如果 Employee 类正在实现接口 ICitizen 和接口 IEmployee，而这两个接口具有相同的索引签名，则需要用到显式接口成员实现。

```
// 在 IEmployee 接口中实现索引器
string IEmployee.this[int index]
{
}
// 在 ICitizen 接口中实现索引器
string ICitizen.this[int index]
{
}
```

#### 5. 属性和索引器之间的比较

索引器与属性相似。 除下表所示的差别外，对属性访问器定义的所有规则也适用于索引器访问器

索引器 类似于属性。 很多时候，创建索引器与创建 属性 所使用的编程语言特性是一样的。 索引器使属性可以被索引：使用一个或多个参数引用的属性。 这些参数为某些值集合提供索引

属性的各种用法同样适用于索引器; 此规则的唯一例外是“自动实现属性”; 编译器无法始终为索引器生成正确的存储

![image.png](https://upload-images.jianshu.io/upload_images/29476859-fe4d382cd2aa2b31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 重载索引器的示例

```
using System;
namespace IndexerApplication
{
   class IndexedNames
   {
      private string[] namelist = new string[size];
      static public int size = 10;
      public IndexedNames()
      {
         for (int i = 0; i < size; i++)
         {
          namelist[i] = "N. A.";
         }
      }
      public string this[int index]
      {
         get
         {
            string tmp;

            if( index >= 0 && index <= size-1 )
            {
               tmp = namelist[index];
            }
            else
            {
               tmp = "";
            }

            return ( tmp );
         }
         set
         {
            if( index >= 0 && index <= size-1 )
            {
               namelist[index] = value;
            }
         }
      }
      public int this[string name]
      {
         get
         {
            int index = 0;
            while(index < size)
            {
               if (namelist[index] == name)
               {
                return index;
               }
               index++;
            }
            return index;
         }

      }

      static void Main(string[] args)
      {
         IndexedNames names = new IndexedNames();
         names[0] = "Zara";
         names[1] = "Riz";
         names[2] = "Nuha";
         names[3] = "Asif";
         names[4] = "Davinder";
         names[5] = "Sunil";
         names[6] = "Rubic";
         // 使用带有 int 参数的第一个索引器
         for (int i = 0; i < IndexedNames.size; i++)
         {
            Console.WriteLine(names[i]);
         }
         // 使用带有 string 参数的第二个索引器
         Console.WriteLine(names["Nuha"]);
         Console.ReadKey();
      }
   }
}

/* Output
Zara
Riz
Nuha
Asif
Davinder
Sunil
Rubic
N. A.
N. A.
N. A.
2
*/
```

ps:

索引器： https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/indexers/using-indexers
