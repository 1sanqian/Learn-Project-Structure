# 一、 Collection集合

Collection集合是专门用于数据存储和检索的类, 有两种主要的集合类型：泛型集合和非泛型集合

构建泛型集合时，接受类型形参，并向改集合**添加项**或者从该集合**删除项**时，无需在Object 类型间来回转换；而非泛型集合将 项 存储为 Obejct, 需要强制转换

### 集合的基本类型

**集合类型表示收集数据的不同方式，例如哈希表、队列、堆栈、包、字典和列表**

所有集合都直接或间接基于 [ICollection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.icollection-1) 或 [ICollection](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.icollection) 接口。 [IList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ilist) 和 [IDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.idictionary) 及其泛型对应项均派生自这两个接口

##### 1. 在基于 IList或直接基于 ICollection的集合中，每个元素都只包含一个值。 这些类型包括：

   *   [Array](https://learn.microsoft.com/zh-cn/dotnet/api/system.array)
   *   [ArrayList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.arraylist)
   *   [List<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1)
   *   [Queue](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.queue)
   *   [ConcurrentQueue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentqueue-1)
   *   [Stack](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.stack)
   *   [ConcurrentStack<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentstack-1)
   *   [LinkedList<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.linkedlist-1)

##### 2. 在基于 IDictionary接口的集合中，每个元素都只包含一个键和一个值。 这些类型包括：

   *   [Hashtable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.hashtable)
   *   [SortedList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.sortedlist)
   *   [SortedList<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sortedlist-2)
   *   [Dictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2)
   *   [ConcurrentDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentdictionary-2)

+ [KeyedCollection<TKey,TItem>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.keyedcollection-2) 类是唯一的，因为它是值中嵌键的值的列表。 因此，它的行为类似列表和字典。

+ 需要高效的多线程集合访问时，请使用 [System.Collections.Concurrent](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent) 命名空间中的泛型集合。

+ [Queue](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.queue) 和 [Queue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.queue-1) 类提供的是先进先出列表。
+  [Stack](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.stack) 和 [Stack<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.stack-1) 类提供的是后进先出列表。

### 各种集合类和用法

下面是各种常用的 System.Collection 命名空间的类

##### 1. 动态数组（[ArrayList](https://github.com/1sanqian/Learn-Project-Structure/blob/ride-doc-structuer-note/main/ArrayList.md)）: 单独索引的对象的有序集合

+ 可以使用索引在指定的位置添加和移动项目
+ 动态数组会自动重新调整大小
+ 允许在列表中进行动态内存分配、增加、搜索、排序各项

##### 2. 哈希表（[Hashtable](https://github.com/1sanqian/Learn-Project-Structure/blob/ride-doc-structuer-note/main/Hashtable.md)）: 使用键来访问集合中的元素

+ 识别键值
+ 哈希表中的每一项都有一个键值对
+ 键用于访问集合中的项目

##### 3. 排序列表（[SortedList](https://github.com/1sanqian/Learn-Project-Structure/blob/ride-doc-structuer-note/main/SortedList.md)）： 使用键和索引来访问列表中的项

+ 排序列表是数组和哈希表的组合
+ 包含一个可使用键或索引访问各项的列表
+ 集合中的各项总是按键值排序

##### 4. 堆栈（[Stack](https://github.com/1sanqian/Learn-Project-Structure/blob/ride-doc-structuer-note/main/Stack.md)）: 后进先出的对象集合

+ 对各项进行后进先出的访问时，就可以使用堆栈
+ 往列表中添加一项为 **推入** 元素
+ 从列表中移除一项为 **弹出** 元素

##### 5. 队列（[Queue](https://github.com/1sanqian/Learn-Project-Structure/blob/ride-doc-structuer-note/main/Queue.md)）: 先进先出的对象集合

+ 对各项进行先进先出的访问，就可以使用队列
+ 往列表中添加一项为 **入队**
+ 从列表中移除一项为 **出队**

##### 6. 点阵列（[BitArray](https://github.com/1sanqian/Learn-Project-Structure/blob/ride-doc-structuer-note/main/BitArray.md)）: 使用值1 和 0 来表示 二进制 数组

+ 需要存储位，但不知道位数，就可以使用点阵列
+ 使用整数索引从点阵列集合中访问各项
+ 索引从零开始

### 不同特征的集合类型

按照不同的特征对不同的集合进行分类

*   **元素访问**：可以枚举每个集合以按顺序访问每个元素。 某些集合可通过*索引*（元素在有序集合中的位置）访问元素；其他集合可按 *键* 访问元素，其中 *值* 与单个 *键* 相关联
*   **性能配置文件**：每个集合都有不同的性能配置文件，可用于添加元素、查找元素或移除元素等操作
*   **动态增长和收缩**：大多数集合支持动态添加或移除元素。 需要注意的是，[Array](https://learn.microsoft.com/zh-cn/dotnet/api/system.array)、[System.Span<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.span-1) 和 [System.Memory<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.memory-1) 不支持

**备注：System.Span<T>属于 [`ref struct`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/ref-struct) 类型，可提供一系列元素的快照，而无需复制这些元素**

##### 1. 可索引集合

可索引集合 是一个可以使用其索引访问每个元素的集合，索引是序列中在它之前的元素数，若按 索引0 引用的元素是第一个元素，索引1  则是第二个元素

创建和初始化字符串列表、移除元素并将元素添加到列表末尾,**每次修改后，都使用 foreach 语句或 for 循环来循环访问字符串**

```

// 1.
// Create a list of strings by using a
// collection initializer.
List<string> salmons = ["chinook", "coho", "pink", "sockeye"];

// Iterate through the list.
foreach (var salmon in salmons)
{
    Console.Write(salmon + " ");
}
// Output: chinook coho pink sockeye

// Remove an element from the list by specifying
// the object.
salmons.Remove("coho");


// Iterate using the index:
for (var index = 0; index < salmons.Count; index++)
{
    Console.Write(salmons[index] + " ");
}
// Output: chinook pink sockeye

// Add the removed element
salmons.Add("coho");
// Iterate through the list.
foreach (var salmon in salmons)
{
    Console.Write(salmon + " ");
}
// Output: chinook pink sockeye coho

// 2.
/*
从一个泛型列表中按索引移除元素
它使用以降序进行循环访问的 `for` 语句，而不是 `foreach` 语句
RemoveAt 方法将导致已移除元素后的元素索引值减小
*/

List<int> numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

// Remove odd numbers.
for (var index = numbers.Count - 1; index >= 0; index--)
{
    if (numbers[index] % 2 == 1)
    {
        // Remove the element by specifying
        // the zero-based index in the list.
        numbers.RemoveAt(index);
    }
}

// Iterate through the list.
// A lambda expression is placed in the ForEach method
// of the List(T) object.
numbers.ForEach( number => Console.Write(number + " "));
// Output: 0 2 4 6 8

// 3.
/*‘
对于 List<T>中的元素类型，还可以定义自己的类
*/
private static void IterateThroughList()
{
    var theGalaxies = new List<Galaxy>
    {
        new (){ Name="Tadpole", MegaLightYears=400},
        new (){ Name="Pinwheel", MegaLightYears=25},
        new (){ Name="Milky Way", MegaLightYears=0},
        new (){ Name="Andromeda", MegaLightYears=3}
    };

    foreach (Galaxy theGalaxy in theGalaxies)
    {
        Console.WriteLine(theGalaxy.Name + "  " + theGalaxy.MegaLightYears);
    }

    // Output:
    //  Tadpole  400
    //  Pinwheel  25
    //  Milky Way  0
    //  Andromeda  3
}

public class Galaxy
{
    public string Name { get; set; }
    public int MegaLightYears { get; set; }
}
```

##### 2. 键值对集合

字典集合，可通过使用每个元素的键访问集合中的元素，最常见的字典集合是 [Dictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2) 类，每次对字典的添加都包含一个值和与其关联的键

+ Dictionary<TKey, TValue> 是一种常用的泛型集合类型，用于存储键值对
+ 基于哈希表实现，可以提供快速的键查找和检索功能
+ 字典中的键必须是唯一的，若尝试使用相同的键添加两次，会导致运行时异常
    + Dictionary<TKey, TValue> 的简单介绍以及基本用法
    ```
    // 1. 声明和初始化一个字典
    using System;
    using System.Collections.Generic;

    class Program
    {
        static void Main()
        {
            // 声明并初始化一个字符串键和整数值的字典
            Dictionary<string, int> ages = new Dictionary<string, int>();

            // 可以使用索引器（`[]`）来添加、修改或获取字典中的值
            // 添加键值对到字典
            ages["Alice"] = 28;
            ages["Bob"] = 30;
            ages["Charlie"] = 25;

            // 或者使用集合初始化器初始化字典
            Dictionary<string, string> colors = new Dictionary<string, string>()
            {
                { "Red", "#FF0000" },
                { "Green", "#00FF00" },
                { "Blue", "#0000FF" }
            };

            // 访问字典中的值
            Console.WriteLine("Alice's age is: " + ages["Alice"]);
            Console.WriteLine("The color code for Green is: " + colors["Green"]);

            // 使用 ContainsKey() 方法 检查字典中是否存在某个键
            if (ages.ContainsKey("Bob"))
            {
                Console.WriteLine("Bob's age is: " + ages["Bob"]);
            }

            // 使用 foreach 循环 遍历字典
            foreach (var pair in colors)
            {
                Console.WriteLine("Key: " + pair.Key + ", Value: " + pair.Value);
            }
        }
    }

    ```

    // 以下的示例稍微比较复杂

    ```
    private static void IterateThruDictionary()
    {
        Dictionary<string, Element> elements = BuildDictionary();
   
         // 使用 ContainsKey 方法和  Dictionary 的 Item[] 属性按键快速查找某个项
         // 使用 `Item` 属性可通过 C# 中的 `elements[symbol]` 来访问 `elements` 集合中的项
         if (elements.ContainsKey(symbol) == false)
         {
              Console.WriteLine(symbol + " not found");
          }
          else
          {
              Element theElement = elements[symbol];
              Console.WriteLine("found: " + theElement.Name);
          }

          // 使用 TryGetValue方法按键快速查找某个项
          if (elements.TryGetValue(symbol, out Element? theElement) == false)
                Console.WriteLine(symbol + " not found");
          else
                Console.WriteLine("found: " + theElement.Name);


        foreach (KeyValuePair<string, Element> kvp in elements)
        {
            Element theElement = kvp.Value;

            Console.WriteLine("key: " + kvp.Key);
            Console.WriteLine("values: " + theElement.Symbol + " " +
                theElement.Name + " " + theElement.AtomicNumber);
        }
    }

    public class Element
    {
        public required string Symbol { get; init; }
        public required string Name { get; init; }
        public required int AtomicNumber { get; init; }
    }

    private static Dictionary<string, Element> BuildDictionary() =>
        new ()
        {
            {"K",
                new (){ Symbol="K", Name="Potassium", AtomicNumber=19}},
            {"Ca",
                new (){ Symbol="Ca", Name="Calcium", AtomicNumber=20}},
            {"Sc",
                new (){ Symbol="Sc", Name="Scandium", AtomicNumber=21}},
            {"Ti",
                new (){ Symbol="Ti", Name="Titanium", AtomicNumber=22}}
        };
    ```

ps ：
 + ContainsKey：https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2.containskey
 + TryGetValue：https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2.trygetvalue
+ Dictionary 的 Item[]：https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2.item

##### 3.迭代器

 迭代器可以是一种方法，或是一个 get 访问器，用于对集合执行自定义迭代，使用 [yield return](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/yield) 语句返回集合的每一个元素，每次返回一个元素

通过使用 foreach 语句调用迭代器，foreach 循环的每次迭代都会调用迭代器， 迭代器中到达 yield return 语句时，会返回一个表达式，并保留当前在代码中的位置。 下次调用迭代器时，将从该位置重新开始执行。

![image.png](https://upload-images.jianshu.io/upload_images/29476859-554bca2da899902e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 4. LINQ 和集合

可以使用语言集成查询 (LINQ) 来访问集合， LINQ 查询提供筛选、排序和分组功能

```
private static void ShowLINQ()
{
    List<Element> elements = BuildList();

    // LINQ Query.
    var subset = from theElement in elements
                 where theElement.AtomicNumber < 22
                 orderby theElement.Name
                 select theElement;

    foreach (Element theElement in subset)
    {
        Console.WriteLine(theElement.Name + " " + theElement.AtomicNumber);
    }

    // Output:
    //  Calcium 20
    //  Potassium 19
    //  Scandium 21
}

private static List<Element> BuildList() => new()
    {
        { new(){ Symbol="K", Name="Potassium", AtomicNumber=19}},
        { new(){ Symbol="Ca", Name="Calcium", AtomicNumber=20}},
        { new(){ Symbol="Sc", Name="Scandium", AtomicNumber=21}},
        { new(){ Symbol="Ti", Name="Titanium", AtomicNumber=22}}
    };
```

### 常用的集合功能

所有集合都提供用于在集合中添加、删除或查找项的方法；此外，所有直接或间接实现 [ICollection](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.icollection) 接口或 [ICollection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.icollection-1) 接口的集合均共享这些功能：

##### 1. 可枚举集合

   + 启用要循环访问的集合，.net可通过实现[System.Collections.IEnumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerable) 或 [System.Collections.Generic.IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1)的接口
   + 可将枚举器看作集合中可指向任何元素的可移动指针
   +  `foreach`, `in`语句和 `For Each...Next`语句可以使用 [GetEnumerator](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerable.getenumerator) 方法公开的枚举器并隐藏操作枚举器的复杂性
   + 任何 实现了`System.Collections.Generic.IEnumerable<T>`的集合均被认为是可查询类型，并可使用 LINQ 对其进行查询

##### 2. 可建集合内容复制到数组

   + 使用 CopyTo 方法将所有集合复制到数组中；但新数组中的元素顺序是以枚举器返回元素的顺序为依据，得到的数组始终是一维数组，下限为零
 
##### 3. 容量和计数属性

+ 集合的容量是它可包含的元素数；集合的计数是它实际所含的元素数
+ 当集合到达当前容量时，大多数集合会知道扩容，重新分配内存并将元素从旧集合复制到新集合
    + 避免因多次重新分配而导致的性能较差的最佳方式是：将初始容量设置为集合的估计大小
    + BitArray 是一种特殊的情况：它的容量与其长度相同，而其长度与其计数相同
    + 例： 对List<T>，如果Count 比 Capacity 少，添加项就是一项O(1)操作；若需添加容量以容纳新元素，则添加项成为O(n)操作，其中 n 是 Count

##### 4. 下限一致

+ 集合的下限是其第一个元素的索引， [System.Collections](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections) 命名空间中的所有索引集合的下限均为零，这表示它们从 0 开始建立索引
+  Array数组默认下限为零，但使用 [Array.CreateInstance](https://learn.microsoft.com/zh-cn/dotnet/api/system.array.createinstance) 创建 Array 类的实例时可定义其他下限

##### 5. 同步以多个线程进行访问（仅System.Collection类）

System.Collections命名空间中的非泛型集合类型通过同步提供一些线程安全性；通常通过 [SyncRoot](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.icollection.syncroot) 和 [IsSynchronized](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.icollection.issynchronized) 成员公开

### 选择集合类

##### 1. 顺序列表访问（检索元素值后是是否丢弃元素）

+ 先进先出 (FIFO)  行为 ：使用 Queue 类或 Queue<T>泛型类
+ 后进先出 (LIFO) 行为：使用 Stack 类或 Stack<T>泛型类
+ 多个线程：并发版本[ConcurrentQueue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentqueue-1) 和 [ConcurrentStack<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentstack-1)
+ 不可变性： 不可变版本 [ImmutableQueue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablequeue-1) 和 [ImmutableStack<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablestack-1)

##### 2. 特定顺序访问

+ 先进先出 (FIFO) : Queue 类以及 Queue<T>、ConcurrentQueue<T>和 ImmutableQueue<T>泛型类
+ 后进先出 (LIFO) : Stack 类以及 Stack<T>、ConcurrentStack<T> 和 ImmutableStack<T> 泛型类
+ 从开头到末尾 / 从末尾到开头:[LinkedList<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.linkedlist-1) 泛型类

##### 3. 按索引访问

+ 按从零开始的元素索引：[ArrayList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.arraylist) 和 [StringCollection](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.stringcollection) 类以及 [List<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1) 泛型类
     + 不可变性: 不可变泛型版本 [ImmutableArray<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablearray-1) 和 [ImmutableList<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablelist-1)

+ 按元素的键：[Hashtable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.hashtable)、[SortedList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.sortedlist)、[ListDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.listdictionary) 和 [StringDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.stringdictionary) 类以及 [Dictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2) 和 [SortedDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sorteddictionary-2) 泛型类
     + 不可变版本：[ImmutableHashSet<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablehashset-1)、[ImmutableDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutabledictionary-2)、[ImmutableSortedSet<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablesortedset-1) 和 [ImmutableSortedDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablesorteddictionary-2)

+ 按从零开始的元素索引 / 元素的键: [NameObjectCollectionBase](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.nameobjectcollectionbase) 和 [NameValueCollection](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.namevaluecollection) 类以及 [KeyedCollection<TKey,TItem>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.keyedcollection-2) 和 [SortedList<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sortedlist-2) 泛型类

##### 4. 每个元素包含（值、键）的组合

+ 包含 一个值 ： 使用任何基于 [IList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ilist) 接口或 [IList<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ilist-1) 泛型接口的集合
    + 不可变选项： [IImmutableList<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.iimmutablelist-1) 泛型接口
+ 包含 一个键和一个值：使用任何基于 [IDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.idictionary) 接口或 [IDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.idictionary-2) 泛型接口的集合
    + 不可变选项： [IImmutableSet<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.iimmutableset-1) 或 [IImmutableDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.iimmutabledictionary-2) 泛型接口
+ 带有 嵌入键 的 值：使用 [KeyedCollection<TKey,TItem>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.keyedcollection-2) 泛型类
+ 一个键 和 多个值：使用 [NameValueCollection](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.namevaluecollection) 类

##### 6. 与输入方式不同的方式排序

+ Hashtable 类按其哈希代码对其元素进行排序
+ 按键对元素进行排序： [SortedList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.sortedlist) 类以及 [SortedList<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sortedlist-2) 和 [SortedDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sorteddictionary-2) 泛型类
+ ArrayList提供了一种 [Sort](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.arraylist.sort) 方法，此方法采用 [IComparer](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.icomparer) 实现作为参数； 其泛型对应项（List<T> 泛型类）提供一种 Sort方法，此方法采用 [IComparer<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.icomparer-1) 泛型接口的实现作为参数

##### 7. 快速搜索和信息检索

+ 小集合
    + ListDictionary速度比 Hashtable 快
    +  Dictionary<TKey,TValue> 泛型类提供比 SortedDictionary<TKey,TValue> 泛型类更快的查找
+ 多线程 ： [ConcurrentDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentdictionary-2)
    +  [ConcurrentBag<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentbag-1) 为无序数据提供快速的多线程插入

##### 8. 只接受字符串的集合

+   [StringCollection](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.stringcollection)（基于 [IList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ilist)）和 [StringDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized.stringdictionary)（基于 [IDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.idictionary)）位于 [System.Collections.Specialized](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.specialized) 命名空间
+ 通过指定其泛型类参数的 [String](https://learn.microsoft.com/zh-cn/dotnet/api/system.string) 类，可以使用 [System.Collections.Generic](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic) 命名空间中的任何泛型集合类作为强类型字符串集合

*如何选择一个集合类：https://learn.microsoft.com/zh-cn/dotnet/standard/collections/selecting-a-collection-class*

### 使用泛型集合的情况

使用泛型集合：可获得类型安全的自动化优点而无需从基集合类型派生和实现特定类型的成员

当集合元素为值类型时，泛型集合类型也通常优于与对应的非泛型集合类型，因为使用泛型不用对元素进行装箱

##### 1. 泛型类型对应于现有的集合类型

*   [List<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1) 泛型类对应于 [ArrayList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.arraylist)
*   [Dictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2) 和 [ConcurrentDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentdictionary-2) 泛型类对应 [Hashtable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.hashtable)
*   [Collection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.collection-1) 泛型类对应于 [CollectionBase](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.collectionbase)。 [Collection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.collection-1) 可以用作基类，但是与 [CollectionBase](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.collectionbase) 不同，它不抽象，这大大降低了其使用难度
*   [ReadOnlyCollection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.readonlycollection-1) 泛型类对应于 [ReadOnlyCollectionBase](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.readonlycollectionbase)。 [ReadOnlyCollection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.readonlycollection-1) 不是抽象的并且拥有可以轻松地公开现有的 [List<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1) 为只读集合的构造函数
*   [Queue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.queue-1)、 [ConcurrentQueue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentqueue-1)、 [ImmutableQueue<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablequeue-1)、 [ImmutableArray<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablearray-1)、[SortedList<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sortedlist-2) 和 [ImmutableSortedSet<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablesortedset-1) 泛型类对应有着相应的相同名称的非泛型类

##### 2. 泛型类型没有对应的非泛型集合类型

*   [LinkedList<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.linkedlist-1) 是一个通用的链接列表，该列表提供 O(1) 插入和删除操作
*   [SortedDictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sorteddictionary-2) 是一个有 O(log `n`) 插入和检索操作的已排序字典，这使它有效代替了 [SortedList<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.sortedlist-2)
*   [KeyedCollection<TKey,TItem>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.objectmodel.keyedcollection-2) 是列表和字典的结合，它提供了一种方法来存储包含自己的键的对象
*   [BlockingCollection<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.blockingcollection-1) 通过限制和阻止功能实现集合类
*   [ConcurrentBag<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.concurrent.concurrentbag-1) 能快速插入和移除未排序元素

##### 不可变生成器

所有不可变的集合类型都提供 Builder 类，可以通过调用非泛型 CreateBuilder() 方法来创建 Builder 对象

通过 Builder 实例，可以调用 ToImmutable()，同样，通过 Immutable集合中，可以调用 ToBuilder() 从泛型不可变集合创建生成器实例，以下是各种 Builder 类型：

*   [ImmutableArray<T>.Builder](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablearray-1.builder)
*   [ImmutableDictionary<TKey,TValue>.Builder](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutabledictionary-2.builder)
*   [ImmutableHashSet<T>.Builder](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablehashset-1.builder)
*   [ImmutableList<T>.Builder](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablelist-1.builder)
*   [ImmutableSortedDictionary<TKey,TValue>.Builder](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablesorteddictionary-2.builder)
*   [ImmutableSortedSet<T>.Builder](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.immutable.immutablesortedset-1.builder)

### 集合内的比较和排序

集合通常使用相等比较器和/或排序比较器

##### 1. 检查元素是否相等

Contains、 IndexOf、 LastIndexOf和 Remove 的方法将相等比较器用于集合元素
若集合时泛型，则按照以下原则比较项是否相等：
+ 如果类型 T 实现 IEquatable<T> 泛型接口，则相等比较器是该接口的Equals 方法
+ 如果类型 T 未实现 IEquatable<T>，则使用Object.Equals

##### 2. 确定排序顺序

BinarySearch 和 Sort 等方法将排序比较器用于集合元素，可在集合的元素间进行比较，或在元素或指定值之间进行比较

默认比较器依赖至少一个正在被比较的对象来实现 **IComparable** 接口。 在用作列表集合中的值，或用作字典集合中的键的所有类上实现 **IComparable** 是一种良好做法。 对泛型集合而言，等同性比较是根据以下内容确定的：

*   如果类型 T 实现 [System.IComparable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.icomparable-1) 泛型接口，则默认比较器是该接口的 [IComparable<T>.CompareTo(T)](https://learn.microsoft.com/zh-cn/dotnet/api/system.icomparable-1.compareto#system-icomparable-1-compareto(-0)) 方法

*   如果类型 T 实现非泛型 [System.IComparable](https://learn.microsoft.com/zh-cn/dotnet/api/system.icomparable) 接口，则默认比较器是该接口的 [IComparable.CompareTo(Object)](https://learn.microsoft.com/zh-cn/dotnet/api/system.icomparable.compareto#system-icomparable-compareto(system-object)) 方法。

*   如果类型 T 未实现任何接口，则没有默认比较器，必须显式提供一个比较器或比较委托

为了提供显式比较，某些方法接受 **IComparer** 实现作为参数。 例如， [List<T>.Sort](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1.sort) 方法接受 [System.Collections.Generic.IComparer<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.icomparer-1) 实现

ps: 

System.Collections 命名空间：https://learn.microsoft.com/zh-cn/dotnet/api/system.collections?view=net-8.0

集合和数据结构：https://learn.microsoft.com/zh-cn/dotnet/standard/collections/

常用的集合类型： https://learn.microsoft.com/zh-cn/dotnet/standard/collections/commonly-used-collection-types?source=recommendations
