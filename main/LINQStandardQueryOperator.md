# LINQ - 标准查询运算符

标准查询运算符是组成 LINQ 模式的方法。 这些方法中的大多数都作用于序列；其中序列指其类型实现 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 接口或 [IQueryable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.iqueryable-1) 接口的对象。 标准查询运算符提供包括筛选、投影、聚合、排序等在内的查询功能

**共有两组 LINQ 标准查询运算符，一组作用于类型 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 的对象，另一组作用于类型 [IQueryable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.iqueryable-1) 的对象**，构成每个集合的方法分别是 [Enumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable) 和 [Queryable](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.queryable) 类的静态成员。 这些方法被定义为作为方法运行目标的类型的*扩展方法*。 可以使用静态方法语法或实例方法语法来调用扩展方法

ps:
+ 查询关键字 ：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/from-clause
+ 标准运算符：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/standard-query-operators-overview

## 在内存中存储查询结果

查询基本上是针对如何检索和组织数据的一套说明。 当请求结果中的每个后续项目时，查询将延迟执行。 使用 foreach 循环访问结果时，项将在受到访问时返回。 若要在不执行 foreach 循环的情况下评估查询并存储其结果，只需调用查询变量上的以下方法之一：
+ [ToList](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.tolist)
+ [ToArray](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.toarray)
+ [ToDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.todictionary)
+ [ToLookup](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.tolookup)

建议在存储查询结果时，将返回的集合对象分配给一个新变量

```
List<int> numbers = [1, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20];

IEnumerable<int> queryFactorsOfFour =
    from num in numbers
    where num % 4 == 0
    select num;

// Store the results in a new variable
// without executing a foreach loop.
var factorsofFourList = queryFactorsOfFour.ToList();

// Read and write from the newly created list to demonstrate that it holds data.
Console.WriteLine(factorsofFourList[2]);
factorsofFourList[2] = 0;
Console.WriteLine(factorsofFourList[2]);
```

在内存中存储查询结果：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/store-the-results-of-a-query-in-memory

## 筛选数据

筛选是指将结果集限制为仅包含满足指定条件的元素的操作。 它也称为选定内容

下图演示了对字符序列进行筛选的结果。 筛选操作的谓词指定字符必须为“A”

![image.png](https://upload-images.jianshu.io/upload_images/29476859-6d70c056d77f6a5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-01fd61cb6f9ce900.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

筛选数据：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/filtering-data

## 投影运算

+ 投影是指将对象转换为一种新形式的操作，该形式通常只包含那些将随后使用的属性
+ 通过使用投影，可以构造从每个对象生成的新类型
+ 可以投影属性，并对该属性执行数学函数
+ 还可以在不更改原始对象的情况下投影该对象

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-6e8c3de6b8e6d3d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 1. Select 

```
List<string> words = ["an", "apple", "a", "day" ];

var query = from word in words
            select word.Substring(0, 1);

foreach (string s in query)
    Console.WriteLine(s);

/* output:
    a
    a
    a
    d
*/
```

#### 2. SelectMany

```
// 使用多个 from 子句来投影字符串列表中每个字符串中的每个单词
List<string> phrases = ["an apple a day", "the quick brown fox"];

var query = from phrase in phrases
            from word in phrase.Split(' ')
            select word;

foreach (string s in query)
    Console.WriteLine(s);

/* output:
    an
    apple
    a
    day
    the
    quick
    brown
    fox
*/
```

#### 3. Zip

Zip 投影运算符有多个重载。 所有 Zip 方法都处理两个或更多可能是异构类型的序列。 前两个重载返回元组，具有来自给定序列的相应位置类型

```
// An int array with 7 elements.
IEnumerable<int> numbers = [1, 2, 3, 4, 5, 6, 7];
// A char array with 6 elements.
IEnumerable<char> letters =['A', 'B', 'C', 'D', 'E', 'F'];
// A string array with 8 elements.
IEnumerable<string> emoji = [ "🤓", "🔥", "🎉", "👀", "⭐", "💜", "✔", "💯"];

foreach ((int number, char letter, string em) in numbers.Zip(letters, emoji))
{
    Console.WriteLine(
        $"Number: {number} is zipped with letter: '{letter}' and emoji: {em}");
}
/* output:
Number: 1 is zipped with letter: 'A' and emoji: 🤓
Number: 2 is zipped with letter: 'B' and emoji: 🔥
Number: 3 is zipped with letter: 'C' and emoji: 🎉
Number: 4 is zipped with letter: 'D' and emoji: 👀
Number: 5 is zipped with letter: 'E' and emoji: ⭐
Number: 6 is zipped with letter: 'F' and emoji: 💜
*/

// 使用前面的 Zip 重载，指定的函数应用于相应的元素 numbers 和 letter，生成 string 结果的序列
foreach (string result in numbers.Zip(letters, (number, letter) =>
 $"{number} = {letter} ({(int)letter})"))
{
    Console.WriteLine(result);
}

/* output:
1 = A (65)
2 = B (66)
3 = C (67)
4 = D (68)
5 = E (69)
6 = F (70)
*/
```

**zip 操作生成的序列的长度永远不会长于最短序列**

*numbers 和 letters 集合的长度不同，生成的序列将省略 numbers 集合中的最后一个元素，因为它没有任何要压缩的内容*

#### 4. Select 与 SelectMany

Select 和 SelectMany 的工作都是依据源值生成一个或多个结果值
+ Select 为每个源值生成一个结果值，所以，总体结果是一个与源集合具有相同元素数目的集合
+ SelectMany 生成单个总体结果，其中包含来自每个源值的串联子集合， 作为参数传递到 SelectMany 的转换函数必须为每个源值返回一个可枚举值序列， 作为参数传递到 SelectMany 的转换函数必须为每个源值返回一个可枚举值序列

举例
```
class Bouquet
{
    public List<string> Flowers { get; set; }
}

static void SelectVsSelectMany()
{
    List<Bouquet> bouquets = 
    [
        new Bouquet { Flowers = new List<string> { "sunflower", "daisy", "daffodil", "larkspur" }},
        new Bouquet { Flowers = new List<string> { "tulip", "rose", "orchid" }},
        new Bouquet { Flowers = new List<string> { "gladiolis", "lily", "snapdragon", "aster", "protea" }},
        new Bouquet { Flowers = new List<string> { "larkspur", "lilac", "iris", "dahlia" }}
    ];

    IEnumerable<List<string>> query1 = bouquets.Select(bq => bq.Flowers);

    IEnumerable<string> query2 = bouquets.SelectMany(bq => bq.Flowers);

    Console.WriteLine("Results by using Select():");
    // Note the extra foreach loop here.
    foreach (IEnumerable<String> collection in query1)
        foreach (string item in collection)
            Console.WriteLine(item);

    Console.WriteLine("\nResults by using SelectMany():");
    foreach (string item in query2)
        Console.WriteLine(item);

    /* This code produces the following output:

       Results by using Select():
        sunflower
        daisy
        daffodil
        larkspur
        tulip
        rose
        orchid
        gladiolis
        lily
        snapdragon
        aster
        protea
        larkspur
        lilac
        iris
        dahlia

       Results by using SelectMany():
        sunflower
        daisy
        daffodil
        larkspur
        tulip
        rose
        orchid
        gladiolis
        lily
        snapdragon
        aster
        protea
        larkspur
        lilac
        iris
        dahlia
    */
}
```

Select 最终返回的是一个集合  `IEnumerable<List<T>>` ，下图描述 Select 如何返回一个与源集合具有相同元素数目的集合

![image.png](https://upload-images.jianshu.io/upload_images/29476859-6d4aebab0c210f22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SelectMany 返回的是一个  `IEnumerable<T>` ，下图描述 SelectMany 如何将中间数组序列串联为一个最终结果值，其中包含每个中间数组中的每个值

![image.png](https://upload-images.jianshu.io/upload_images/29476859-fedc66fbc220a1ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ps：投影运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/projection-operations

## 集运算

LINQ 中的集运算是指根据相同或不同集合（或集）中是否存在等效元素来生成结果集的查询运算

### 方法
![image.png](https://upload-images.jianshu.io/upload_images/29476859-3411adcab6410518.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### Distinct 和 DistinctBy

字符串序列上 [Enumerable.Distinct](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.distinct) 方法的行为。 返回的序列包含输入序列的唯一元素

![image.png](https://upload-images.jianshu.io/upload_images/29476859-da1ed111bcd28c2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
string[] planets = ["Mercury", "Venus", "Venus", "Earth", "Mars", "Earth"];

IEnumerable<string> query = from planet in planets.Distinct()
                            select planet;

foreach (var str in query)
{
    Console.WriteLine(str);
}

/* output:
 * Mercury
 * Venus
 * Earth
 * Mars
 */

namespace SolarSystem;

record Planet(
    string Name,
    PlanetType Type,
    int OrderFromSun)
{
    public static readonly Planet Mercury =
        new(nameof(Mercury), PlanetType.Rock, 1);

    public static readonly Planet Venus =
        new(nameof(Venus), PlanetType.Rock, 2);

    public static readonly Planet Earth =
        new(nameof(Earth), PlanetType.Rock, 3);

    public static readonly Planet Mars =
        new(nameof(Mars), PlanetType.Rock, 4);

    public static readonly Planet Jupiter =
        new(nameof(Jupiter), PlanetType.Gas, 5);

    public static readonly Planet Saturn =
        new(nameof(Saturn), PlanetType.Gas, 6);

    public static readonly Planet Uranus =
        new(nameof(Uranus), PlanetType.Liquid, 7);

    public static readonly Planet Neptune =
        new(nameof(Neptune), PlanetType.Liquid, 8);

    // Yes, I know... not technically a planet anymore
    public static readonly Planet Pluto =
        new(nameof(Pluto), PlanetType.Ice, 9);
}
```

`DistinctBy` 是 `Distinct` 的替代方法，它采用 `keySelector`。 `keySelector` 用作源类型的比较鉴别器

```
Planet[] planets =
[
    Planet.Mercury,
    Planet.Venus,
    Planet.Earth,
    Planet.Mars,
    Planet.Jupiter,
    Planet.Saturn,
    Planet.Uranus,
    Planet.Neptune,
    Planet.Pluto
];

// 在下面的代码中，行星根据其 PlanetType 进行区分，并显示每种类型的第一个行星
foreach (Planet planet in planets.DistinctBy(p => p.Type))
{
    Console.WriteLine(planet);
}

/* output:
Planet { Name = Mercury, Type = Rock, OrderFromSun = 1 }
Planet { Name = Jupiter, Type = Gas, OrderFromSun = 5 }
Planet { Name = Uranus, Type = Liquid, OrderFromSun = 7 }
Planet { Name = Pluto, Type = Ice, OrderFromSun = 9 }
Planet 阵列按每个唯一行星类型的第一个匹配项进行不同的筛选
将生成的 planet 实例写入控制台
*/
```

#### Except 和 ExceptBy

 返回的序列只包含位于第一个输入序列但不位于第二个输入序列的元素

![image.png](https://upload-images.jianshu.io/upload_images/29476859-1f52e461b54452bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
string[] planets1 = ["Mercury", "Venus", "Earth", "Jupiter"];
string[] planets2 = ["Mercury", "Earth", "Mars", "Jupiter"];

IEnumerable<string> query = from planet in planets1.Except(planets2)
                            select planet;

foreach (var str in query)
{
    Console.WriteLine(str);
}

/* This code produces the following output:
 *
 * Venus
 */
```

[ExceptBy](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.exceptby) 方法是 `Except` 的替代方法，它采用可能是异构类型的两个序列和一个 `keySelector`。 `keySelector` 与第二个集合的类型相同，用作源类型的比较鉴别器

```
// A shared "keySelector"
static string PlanetNameSelector(Planet planet) => planet.Name;

foreach (Planet planet in
    planets.ExceptBy(
        morePlanets.Select(PlanetNameSelector), PlanetNameSelector))
{
    Console.WriteLine(planet);
}

/* output:
Planet { Name = Venus, Type = Rock, OrderFromSun = 2 }
keySelector 定义为 static 局部函数，用于区分行星名称 
第一个行星阵列会根据名称筛选为第二个行星阵列中未找到的行星
将生成的 planet 实例写入控制台
*/
```

#### Intersect 和 IntersectBy

返回的序列包含两个输入序列共有的元素

![image.png](https://upload-images.jianshu.io/upload_images/29476859-f0e1f8d32eae997e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
string[] planets1 = ["Mercury", "Venus", "Earth", "Jupiter"];
string[] planets2 = ["Mercury", "Earth", "Mars", "Jupiter"];

IEnumerable<string> query = from planet in planets1.Intersect(planets2)
                            select planet;

foreach (var str in query)
{
    Console.WriteLine(str);
}

/*  output:
Mercury
Earth
Jupiter
 */
```

[IntersectBy](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.intersectby) 方法是 `Intersect` 的替代方法，它采用可能是异构类型的两个序列和一个 `keySelector`。 `keySelector` 用作第二个集合类型的比较鉴别器

```
Planet[] firstFivePlanetsFromTheSun =
[
    Planet.Mercury,
    Planet.Venus,
    Planet.Earth,
    Planet.Mars,
    Planet.Jupiter
];

Planet[] lastFivePlanetsFromTheSun =
[
    Planet.Mars,
    Planet.Jupiter,
    Planet.Saturn,
    Planet.Uranus,
    Planet.Neptune
];

foreach (Planet planet in
    firstFivePlanetsFromTheSun.IntersectBy(
        lastFivePlanetsFromTheSun, planet => planet))
{
    Console.WriteLine(planet);
}

/* output:
Planet { Name = Mars, Type = Rock, OrderFromSun = 4 }
Planet { Name = Jupiter, Type = Gas, OrderFromSun = 5 }
这两个 Planet 阵列通过它们的值比较语义相交
只有在这两个阵列中都找到的行星才会出现在结果序列中
将生成的 planet 实例写入控制台
*/
```

#### Union 和 UnionBy

返回的序列包含两个输入序列的唯一元素

![image.png](https://upload-images.jianshu.io/upload_images/29476859-13c42986ceda9951.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
string[] planets1 = ["Mercury", "Venus", "Earth", "Jupiter"];
string[] planets2 = ["Mercury", "Earth", "Mars", "Jupiter"];

IEnumerable<string> query = from planet in planets1.Union(planets2)
                            select planet;

foreach (var str in query)
{
    Console.WriteLine(str);
}

/* output:
Mercury
Venus
Earth
Jupiter
Mars
 */
```

[UnionBy](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.unionby) 方法是 `Union` 的替代方法，它采用相同类型的两个序列和一个 `keySelector`。 `keySelector` 用作源类型的比较鉴别器

```
foreach (Planet planet in
    firstFivePlanetsFromTheSun.UnionBy(
        lastFivePlanetsFromTheSun, planet => planet))
{
    Console.WriteLine(planet);
}

/* output:
Planet { Name = Mercury, Type = Rock, OrderFromSun = 1 }
Planet { Name = Venus, Type = Rock, OrderFromSun = 2 }
Planet { Name = Earth, Type = Rock, OrderFromSun = 3 }
Planet { Name = Mars, Type = Rock, OrderFromSun = 4 }
Planet { Name = Jupiter, Type = Gas, OrderFromSun = 5 }
Planet { Name = Saturn, Type = Gas, OrderFromSun = 6 }
Planet { Name = Uranus, Type = Liquid, OrderFromSun = 7 }
Planet { Name = Neptune, Type = Liquid, OrderFromSun = 8 }
这两个 Planet 阵列使用它们的 record 值比较语义交织在一起
将生成的 planet 实例写入控制台
*/
```

ps：集运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/set-operations

## 排序

排序操作基于一个或多个属性对序列的元素进行排序。 第一个排序条件对元素执行主要排序。 通过指定第二个排序条件，可以对每个主要排序组内的元素进行排序，下图按字母顺序排序

![image.png](https://upload-images.jianshu.io/upload_images/29476859-193efde0e5bb59aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-70879289ce77871e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.OrderBy

 LINQ 查询中使用 orderby 子句按字符串长度对数组中的字符串进行升序排序

```
string[] words = ["the", "quick", "brown", "fox", "jumps"];  
  
IEnumerable<string> query = from word in words  
                            orderby word.Length  
                            select word;  
  
foreach (string str in query)  
    Console.WriteLine(str);  
  
/*  output:  
    the  
    fox  
    quick  
    brown  
    jumps  
*/
```

#### 2. OrderByDescending

LINQ 查询中使用 orderby descending 子句按字符串的第一个字母对字符串进行降序排序

```
string[] words = ["the", "quick", "brown", "fox", "jumps"];  
  
IEnumerable<string> query = from word in words  
                            orderby word.Substring(0, 1) descending  
                            select word;  
  
foreach (string str in query)  
    Console.WriteLine(str);  
  
/* output:  
    the  
    quick  
    jumps  
    fox  
    brown  
*/
```

#### 3. ThenBy

LINQ 查询中使用 orderby 子句对数组中的字符串执行主要和次要排序

```
//  首先按字符串长度，其次按字符串的第一个字母，对字符串进行升序排序
string[] words = ["the", "quick", "brown", "fox", "jumps"];  
  
IEnumerable<string> query = from word in words  
                            orderby word.Length, word.Substring(0, 1)  
                            select word;  
  
foreach (string str in query)  
    Console.WriteLine(str);  
  
/* output:  
    fox  
    the  
    brown  
    jumps  
    quick  
*/
```

#### 4. ThenByDescending	

LINQ 查询中使用 orderby descending 子句按升序执行主要排序，按降序执行次要排序

```
//  首先按字符串长度，其次按字符串的第一个字母，对字符串进行排序
string[] words = ["the", "quick", "brown", "fox", "jumps"];  
  
IEnumerable<string> query = from word in words  
                            orderby word.Length, word.Substring(0, 1) descending  
                            select word;  
  
foreach (string str in query)  
    Console.WriteLine(str);  
  
/* output:  
    the  
    fox  
    quick  
    jumps  
    brown  
*/
```

ps: 排序： https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/sorting-data

## 限定符运算

限定符运算返回一个 [Boolean](https://learn.microsoft.com/zh-cn/dotnet/api/system.boolean) 值，该值指示序列中是否有一些元素满足条件或是否所有元素都满足条件

![image.png](https://upload-images.jianshu.io/upload_images/29476859-b8a5ae6668c1b163.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-e9ed5d18beda0fa7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1. All

```
// 使用 All 检查所有字符串是否为特定长度
class Market
{
    public string Name { get; set; }
    public string[] Items { get; set; }
}

public static void Example()
{
    List<Market> markets =
    [
        new Market { Name = "Emily's", Items = ["kiwi", "cheery", "banana"] },
        new Market { Name = "Kim's", Items = ["melon", "mango", "olive"] },
        new Market { Name = "Adam's", Items = ["kiwi", "apple", "orange"] },
    ];

    // Determine which market have all fruit names length equal to 5
    IEnumerable<string> names = from market in markets
                                where market.Items.All(item => item.Length == 5)
                                select market.Name;

    foreach (string name in names)
    {
        Console.WriteLine($"{name} market");
    }

    // output:
    // Kim's market
}
```

#### 2. Any

```
// 使用 Any 检查所有字符串是否以“o”开头
class Market
{
    public string Name { get; set; }
    public string[] Items { get; set; }
}

public static void Example()
{
    List<Market> markets =
    [
        new Market { Name = "Emily's", Items = ["kiwi", "cheery", "banana"] },
        new Market { Name = "Kim's", Items = ["melon", "mango", "olive"] },
        new Market { Name = "Adam's", Items = ["kiwi", "apple", "orange"] },
    ];

    // Determine which market have any fruit names start with 'o'
    IEnumerable<string> names = from market in markets
                                where market.Items.Any(item => item.StartsWith("o"))
                                select market.Name;

    foreach (string name in names)
    {
        Console.WriteLine($"{name} market");
    }

    // output:
    // Kim's market
    // Adam's market
}
```

#### 3. Contains

```
// 使用 Contains 检查所有数组是否具有特定元素
class Market
{
    public string Name { get; set; }
    public string[] Items { get; set; }
}

public static void Example()
{
    List<Market> markets =
    [
        new Market { Name = "Emily's", Items = ["kiwi", "cheery", "banana"] },
        new Market { Name = "Kim's", Items = ["melon", "mango", "olive"] },
        new Market { Name = "Adam's", Items = ["kiwi", "apple", "orange"] },
    ];

    // Determine which market contains fruit names equal 'kiwi'
    IEnumerable<string> names = from market in markets
                                where market.Items.Contains("kiwi")
                                select market.Name;

    foreach (string name in names)
    {
        Console.WriteLine($"{name} market");
    }

    // output:
    // Emily's market
    // Adam's market
}
```

ps: 限定符运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/quantifier-operations

## 数据分区运算

LINQ 中的分区是指将输入序列划分为两个部分的操作，无需重新排列元素，然后返回其中一个部分

下图显示对字符序列进行三种不同的分区操作的结果。 第一个操作返回序列中的前三个元素。 第二个操作跳过前三个元素，返回剩余元素。 第三个操作跳过序列中的前两个元素，返回接下来的三个元素

![image.png](https://upload-images.jianshu.io/upload_images/29476859-092f3233265da150.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-7c43b89e18458ec7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// 比较典型的是使用 Skip 和 Take 进行分页
// currentPage 当前页码
// PageSize 一页数据容量
var data = list.Skip((currentPage - 1) * PageSize).Take(PageSize).ToList()
```

#### 1. Chunk 

运算符用于根据给定的 size 拆分序列的元素

```
 int chunkNumber = 1;  // 初始化块编号为1

// 使用 LINQ 的 Chunk 扩展方法将范围为0到7的整数按照大小为3的块进行分割
// Chunk 方法将返回一个 IEnumerable<int[]>，其中每个 int[] 数组代表一个分割后的块
foreach (int[] chunk in Enumerable.Range(0, 8).Chunk(3))
{
    Console.WriteLine($"Chunk {chunkNumber++}:"); // 打印当前块的编号
    foreach (int item in chunk)  // 遍历当前块中的每个元素并打印
    {
        Console.WriteLine($"    {item}");
    }
    Console.WriteLine(); // 打印空行，用于区分不同的块
    // 每次循环块编号自增
}
/* output:
 Chunk 1:
    0
    1
    2

Chunk 2:
    3
    4
    5

Chunk 3:
    6
    7
*
*
关于代码中的 Enumerable.Range(0, 8).Chunk(3)
这里假设存在一个自定义的 Chunk 扩展方法，它将范围为 0 到 7 的整数按照大小为 3 的块进行分割
这个扩展方法将返回一个 IEnumerable<int[]>，其中每个 int[] 数组代表一个分割后的块
因此，在 foreach 循环中，针对每个块都会执行循环，并在控制台输出每个块的编号和其中的元素

依赖于 Enumerable.Range(Int32, Int32)生成数字序列
应用 `Chunk` 运算符，将序列拆分为最大大小为 3 的块

*/
```


ps: 数据分区运算：https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.queryable.skip?view=net-8.0

## 生成运算

生成是指创建新的值序列

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-91b42b25fe688c21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意： DefaultlfEmpty**
+ DefaultlfEmpty 多用于数据库的左外链接
    + 原始集合若不为空，则返回包含原始集合元素的新序列
    + 原始集合若为空，DefaultlfEmpty 是否有设置默认值及返回类型决定
        + 若 DefaultlfEmpty 有设置默认值，则返回默认值
        + 若 DefaultlfEmpty 没有设置默认值，根据返回类型
             + string 类型，返回 null
             + int 类型，返回 0
             + decimal 类型，返回 0M 
             + bool 类型，返回 false
             + 复杂类型，返回默认复杂类型初始化序列
```
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}
List<Person> emptyList = new List<Person>();
var result = emptyList.DefaultIfEmpty(); // 如果集合为空，返回包含默认初始化的 Person 对象的序列
// 返回的 result 序列将包含一个 null（因为 Person 类型的默认初始化是 null）
```

ps: 生成运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/generation-operations

## 相等运算

两个序列，其相应元素相等且具有被视为相等的相同数量的元素

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-23cc3cc29c8d1a74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
class Pet
{
    public string Name { get; set; }
    public int Age { get; set; }
}

public static void SequenceEqualEx1()
{
    Pet pet1 = new Pet { Name = "Turbo", Age = 2 };
    Pet pet2 = new Pet { Name = "Peanut", Age = 8 };

    // Create two lists of pets.
    List<Pet> pets1 = new List<Pet> { pet1, pet2 };
    List<Pet> pets2 = new List<Pet> { pet1, pet2 };

    bool equal = pets1.SequenceEqual(pets2);

    Console.WriteLine(
        "The lists {0} equal.",
        equal ? "are" : "are not");
}

/*output:
 The lists are equal.
*/
```

ps: 相等运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/equality-operations

## 元素运算

元素运算从序列中返回唯一、特定的元素

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-c9896e2f7255002b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
List<int> months = new List<int> { };

// Setting the default value to 1 after the query.
int firstMonth1 = months.FirstOrDefault();
if (firstMonth1 == 0)
{
    firstMonth1 = 1;
}
Console.WriteLine("The value of the firstMonth1 variable is {0}", firstMonth1);

// Setting the default value to 1 by using DefaultIfEmpty() in the query.
int firstMonth2 = months.DefaultIfEmpty(1).First();
Console.WriteLine("The value of the firstMonth2 variable is {0}", firstMonth2);

/* output:
 The value of the firstMonth1 variable is 1
 The value of the firstMonth2 variable is 1
*/
```

ps: 元素运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/element-operations

## 转换数据类型

元素运算从序列中返回唯一、特定的元素

转换方法可更改输入对象的类型

LINQ 查询中的转换运算可用于各种应用程序。 以下是一些示例：

+ [Enumerable.AsEnumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.asenumerable) 方法可用于隐藏类型的标准查询运算符自定义实现
+ [Enumerable.OfType](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.oftype) 方法可用于为 LINQ 查询启用非参数化集合
+ [Enumerable.ToArray](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.toarray)、[Enumerable.ToDictionary](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.todictionary)、[Enumerable.ToList](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.tolist) 和 [Enumerable.ToLookup](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.tolookup) 方法可用于强制执行即时的查询，而不是将其推迟到枚举该查询时

### 方法

下表列出了执行数据类型转换的标准查询运算符方法

本表中名称以“As”开头的转换方法可更改源集合的静态类型，但不对其进行枚举。 名称以“To”开头的方法可枚举源集合，并将项放入相应的集合类型

![image.png](https://upload-images.jianshu.io/upload_images/29476859-c32181f3acbd13b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 查询表达式语法示例

```
class Plant
{
    public string Name { get; set; }
}

class CarnivorousPlant : Plant
{
    public string TrapType { get; set; }
}

static void Cast()
{
    Plant[] plants = 
    [
        new CarnivorousPlant { Name = "Venus Fly Trap", TrapType = "Snap Trap" },
        new CarnivorousPlant { Name = "Pitcher Plant", TrapType = "Pitfall Trap" },
        new CarnivorousPlant { Name = "Sundew", TrapType = "Flypaper Trap" },
        new CarnivorousPlant { Name = "Waterwheel Plant", TrapType = "Snap Trap" }
    ];

    // 使用显式类型化的范围变量将类型 Plant 转换为子类型 CarnivorousPlant，然后才访问仅在此子类型上可用的成员
    var query = from CarnivorousPlant cPlant in plants
                where cPlant.TrapType == "Snap Trap"
                select cPlant;

    foreach (Plant plant in query)
        Console.WriteLine(plant.Name);

    /* output:
        Venus Fly Trap
        Waterwheel Plant
    */
}
```

ps: 转换数据类型：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/converting-data-types

## 串联运算

串联是指将一个序列附加到另一个序列的操作

![image.png](https://upload-images.jianshu.io/upload_images/29476859-a38dc6f5528d8284.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 方法
 
![image.png](https://upload-images.jianshu.io/upload_images/29476859-b7868b7bd66109cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
class Pet
{
    public string Name { get; set; }
    public int Age { get; set; }
}

static Pet[] GetCats()
{
    Pet[] cats = { new Pet { Name="Barley", Age=8 },
                   new Pet { Name="Boots", Age=4 },
                   new Pet { Name="Whiskers", Age=1 } };
    return cats;
}

static Pet[] GetDogs()
{
    Pet[] dogs = { new Pet { Name="Bounder", Age=3 },
                   new Pet { Name="Snoopy", Age=14 },
                   new Pet { Name="Fido", Age=9 } };
    return dogs;
}

public static void ConcatEx1()
{
    Pet[] cats = GetCats();
    Pet[] dogs = GetDogs();

    IEnumerable<string> query =
        cats.Select(cat => cat.Name).Concat(dogs.Select(dog => dog.Name));

    foreach (string name in query)
    {
        Console.WriteLine(name);
    }
}

/* output:
Barley
Boots
Whiskers
Bounder
Snoopy
Fido
*/
```

ps: 串联运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/concatenation-operations

## 聚合运算

聚合运算从值的集合中计算出单个值

![](https://upload-images.jianshu.io/upload_images/29476859-bd8fde2d8c625955.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 方法
 
![image.png](https://upload-images.jianshu.io/upload_images/29476859-c02f112b563fc3d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ps:  聚合运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/aggregation-operations

## 联接运算（Join）

联接两个数据源就是将一个数据源中的对象与另一个数据源中具有相同公共属性的对象相关联

当查询所面向的数据源相互之间具有无法直接领会的关系时，Join 就成为一项重要的运算

INQ 框架中提供的 join 方法包括 [Join](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.join) 和 [GroupJoin](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.groupjoin)
+  [Join](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.join) 实现了内部联接，这种联接只返回那些在另一个数据集中具有匹配项的对象
+  [GroupJoin](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.groupjoin) 方法在关系数据库术语中没有直接等效项，但实现了内部联接和左外部联接的超集
+ 左外部联接是指返回第一个（左侧）数据源的每个元素的联接，即使其他数据源中没有关联元素

### 方法

![image.png](https://upload-images.jianshu.io/upload_images/29476859-f907224cd04ec60f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 示例

##### 1. join

使用 join … in … on … equals … 子句基于特定值联接两个序列

##### 2. GroupJoin

用 join … in … on … equals … into … 子句基于特定值联接两个序列，并对每个元素的结果匹配项进行分组

```
class Product
{
    public string? Name { get; set; }
    public int CategoryId { get; set; }
}

class Category
{
    public int Id { get; set; }
    public string? CategoryName { get; set; }
}

public static void Example()
{
    List<Product> products =
    [
        new Product { Name = "Cola", CategoryId = 0 },
        new Product { Name = "Tea", CategoryId = 0 },
        new Product { Name = "Apple", CategoryId = 1 },
        new Product { Name = "Kiwi", CategoryId = 1 },
        new Product { Name = "Carrot", CategoryId = 2 },
    ];

    List<Category> categories =
    [
        new Category { Id = 0, CategoryName = "Beverage" },
        new Category { Id = 1, CategoryName = "Fruit" },
        new Category { Id = 2, CategoryName = "Vegetable" }
    ];

    // 1. Join 
    var query = from product in products
                join category in categories on product.CategoryId equals category.Id
                select new { product.Name, category.CategoryName };
  foreach (var item in query)
    {
        Console.WriteLine($"{item.Name} - {item.CategoryName}");
    }

  // 2.  GroupJoin
  var productGroups = from category in categories
                        join product in products on category.Id equals product.CategoryId into productGroup
                        select productGroup;
    foreach (IEnumerable<Product> productGroup in productGroups)
    {
        Console.WriteLine("Group");
        foreach (Product product in productGroup)
        {
            Console.WriteLine($"{product.Name,8}");
        }
    }

/* output:
Cola - Beverage
Tea - Beverage
Apple - Fruit
Kiwi - Fruit
Carrot - Vegetable

Group
Cola
Tea
Group
Apple
Kiwi
Group
Carrot
*/
}
```

ps:  联接运算：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/join-operations

### 内部联接

内部联接会生成一个结果集，在该结果集中，第一个集合的每个元素对于第二个集合中的每个匹配元素都会出现一次。 如果第一个集合中的元素没有匹配元素，则它不会出现在结果集中。 由 C# 中的 `join` 子句调用的 [Join](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.join) 方法可实现内部联接

#### 示例：简单键联接

```
var query =
    from person in people
    join pet in pets on person equals pet.Owner
    select new
    {
        OwnerName = person.FirstName,
        PetName = pet.Name
    };

// 使用 Join 方法语法实现相同的结果
var query =
    people.Join(pets,
                person => person,
                pet => pet.Owner,
                (person, pet) =>
                    new { OwnerName = person.FirstName, PetName = pet.Name });
```

### 分组联接

分组联接会创建中间组的集合

```
  var query1 =
    from person in people
    join pet in pets on person equals pet.Owner into gj
    from subpet in gj
    select new
    {
        OwnerName = person.FirstName,
        PetName = subpet.Name
    };
// 等效
var query2 =
    from person in people
    join pet in pets on person equals pet.Owner
    select new
    {
        OwnerName = person.FirstName,
        PetName = pet.Name
    };

// 可以使用 GroupJoin方法实现相同的结果
var query1 = people.GroupJoin(pets,
        person => person,
        pet => pet.Owner,
        (person, gj) => new { person, gj })
    .SelectMany(pet => pet.gj,
        (groupJoinPet, subpet) => new { OwnerName = groupJoinPet.person.FirstName, PetName = subpet.Name });

// Join
var query2 = people.Join(pets,
    person => person,
    pet => pet.Owner,
    (person, pet) => new { OwnerName = person.FirstName, PetName = pet.Name });
```

ps： 分组联接：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/perform-grouped-joins

###  多联接

可以将任意数量的联接操作相互追加，以执行多联接

```
var query =
    from person in people
    join cat in cats on person equals cat.Owner
    join dog in dogs on new
    {
        Owner = person,
        Letter = cat.Name[..1]
    } equals new
    {
        dog.Owner,
        Letter = dog.Name[..1]
    }
    select new
    {
        CatName = cat.Name,
        DogName = dog.Name
    };
// 使用多个 Join方法的等效方法对匿名类型使用同一方法
var query = people.Join(cats,
        person => person,
        cat => cat.Owner,
        (person, cat) => new { person, cat })
    .Join(dogs,
        commonOwner => new { Owner = commonOwner.person, Letter = commonOwner.cat.Name[..1] },
        dog => new { dog.Owner, Letter = dog.Name[..1] },
        (commonOwner, dog) => new { CatName = commonOwner.cat.Name, DogName = dog.Name });
```

### 左外部联接

外部联接是这样定义的：返回第一个集合的每个元素，无论该元素在第二个集合中是否有任何相关元素。 可以使用 LINQ 通过对分组联接的结果调用 [DefaultIfEmpty](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.defaultifempty) 方法来执行左外部联接

```
var query =
    from person in people
    join pet in pets on person equals pet.Owner into gj
    from subpet in gj.DefaultIfEmpty()
    select new
    {
        person.FirstName,
        PetName = subpet?.Name ?? string.Empty
    };
```

ps：左外部联接：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/perform-left-outer-joins

### 对Join子句的结果进行排序

```
var groupJoinQuery2 =
    from category in categories
    join prod in products on category.ID equals prod.CategoryID into prodGroup
    orderby category.Name
    select new
    {
        Category = category.Name,
        Products =
            from prod2 in prodGroup
            orderby prod2.Name
            select prod2
    };
```

ps：对Join子句的结果进行排序：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/order-the-results-of-a-join-clause

### 组合键联接

可以使用复合键基于多个属性来比较元素，而不是只基于一个属性使元素相关联

```
var query =
    from employee in employees
    join student in students on new
    {
        employee.FirstName,
        employee.LastName
    } equals new
    {
        student.FirstName,
        student.LastName
    }
    select employee.FirstName + " " + employee.LastName;

// 使用 Join 方法
var query = employees.Join(
     students,
     employee => new { FirstName = employee.FirstName, LastName = employee.LastName },
     student => new { FirstName = student.FirstName, student.LastName },
     (employee, student) => $"{employee.FirstName} {employee.LastName}"
 );

// 组合键联接3个表
var query = from o in db.Orders
    from p in db.Products
    join d in db.OrderDetails
        on new {o.OrderID, p.ProductID} equals new {d.OrderID, d.ProductID} into details
        from d in details
        select new {o.OrderID, p.ProductID, d.UnitPrice};
```

ps: 组合键联接： https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/join-by-using-composite-keys

### 自定义联接

 在查询表达式中，join 子句只限于同等联接，但是，在以下情况下不能使用 join 子句：
+ 当联接依据不等式表达式时（非同等联接）
+ 当联接依据多个等式或不等式表达式时
+ 当必须为联接操作前的右侧（内部）序列引入临时范围变量

#### 交叉联接
 
```
 var crossJoinQuery =
    from c in categories
    from p in products
    select new
    {
        c.ID,
        p.Name
    };
```

#### 非等同联接

```
var nonEquijoinQuery =
    from p in products
    let catIds =
        from c in categories
        select c.ID
    where catIds.Contains(p.CategoryID)
    select new
    {
        Product = p.Name,
        p.CategoryID
    };
```

#### 合并 CSV 文件

```
var names = File.ReadAllLines("csv/names.csv");
var scores = File.ReadAllLines("csv/scores.csv");

IEnumerable<Student> queryNamesScores =
    // Split each line in the data files into an array of strings.
    from name in names
    let x = name.Split(',')
    from score in scores
    let s = score.Split(',')
    // Look for matching IDs from the two data files.
    where x[2] == s[0]
    // If the IDs match, build a Student object.
    select new Student(
        FirstName: x[0],
        LastName: x[1],
        StudentID: int.Parse(x[2]),
        ExamScores: (
            from scoreAsText in s.Skip(1)
            select int.Parse(scoreAsText)
        ).ToList()
    );

foreach (var student in queryNamesScores)
{
    Console.WriteLine($"The average score of {student.FirstName} {student.LastName} is {student.ExamScores.Average()}.");
}

/* Output:
    The average score of Omelchenko Svetlana is 82.5.
    The average score of O'Donnell Claire is 72.25.
    The average score of Mortensen Sven is 84.5.
    The average score of Garcia Cesar is 88.25.
    The average score of Garcia Debra is 67.
    The average score of Fakhouri Fadi is 92.25.
    The average score of Feng Hanying is 88.
    The average score of Garcia Hugo is 85.75.
    The average score of Tucker Lance is 81.75.
    The average score of Adams Terry is 85.25.
    The average score of Zabokritski Eugene is 83.
    The average score of Tucker Michael is 92.
 */
```

ps: 自定义联接： https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/perform-custom-join-operations

## 数据分组

分组是指将数据分到不同的组，使每组中的元素拥有公共的属性；

分组是 LINQ 最强大的功能之一。 可按各种方式对数据进行分组：
+ 依据单个属性
+ 依据字符串属性的首字母
+ 依据计算出的数值范围
+ 依据布尔谓词或其他表达式
+ 依据组合键

![image.png](https://upload-images.jianshu.io/upload_images/29476859-9746a8a95d334e99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 方法

![pps](https://upload-images.jianshu.io/upload_images/29476859-5733bc80f5382f2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ps： 数据分组： https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/grouping-data

### 对查询结果分组

#### 按单个属性分组示例

使用元素的单个属性作为分组键对源元素进行分组

```
var groupByLastNamesQuery =
    from student in students
    group student by student.LastName into newGroup
    orderby newGroup.Key
    select newGroup;
```

#### 按值分组示例

使用除对象属性以外的某个项作为分组键对源元素进行分组

```
var groupByFirstLetterQuery =
    from student in students
    group student by student.LastName[0];
```

#### 按范围分组示例

通过使用某个数值范围作为分组键对源元素进行分组

```
var groupByPercentileQuery =
    from student in students
    let percentile = GetPercentile(student)
    group new
    {
        student.FirstName,
        student.LastName
    } by percentile into percentGroup
    orderby percentGroup.Key
    select percentGroup;
```

#### 按比较分组示例

通过使用布尔比较表达式对源元素进行分组

```
var groupByHighAverageQuery =
    from student in students
    group new
    {
        student.FirstName,
        student.LastName
    } by student.ExamScores.Average() > 75 into studentGroup
    select studentGroup;
```

#### 按匿名类型分组

使用匿名类型来封装包含多个值的键

```
var groupByCompoundKey =
    from student in students
    group student by new
    {
        FirstLetterOfLastName = student.LastName[0],
        IsScoreOver85 = student.ExamScores[0] > 85
    } into studentGroup
    orderby studentGroup.Key.FirstLetterOfLastName
    select studentGroup;
```

ps： 对查询结果分组： https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/group-query-results

### 创建嵌套组

```
var nestedGroupsQuery =
    from student in students
    group student by student.Year into newGroup1
    from newGroup2 in
    from student in newGroup1
    group student by student.LastName
    group newGroup2 by newGroup1.Key;
// 需要使用 3 个嵌套的 foreach 循环来循环访问嵌套组的内部元素
foreach (var outerGroup in nestedGroupsQuery)
{
    Console.WriteLine($"DataClass.Student Level = {outerGroup.Key}");
    foreach (var innerGroup in outerGroup)
    {
        Console.WriteLine($"\tNames that begin with: {innerGroup.Key}");
        foreach (var innerGroupElement in innerGroup)
        {
            Console.WriteLine($"\t\t{innerGroupElement.LastName} {innerGroupElement.FirstName}");
        }
    }
}
```

ps： 创建嵌套组： https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/create-a-nested-group

### 对分组操作执行子查询

```
var queryGroupMax =
    from student in students
    group student by student.Year into studentGroup
    select new
    {
        Level = studentGroup.Key,
        HighestScore = (
            from student2 in studentGroup
            select student2.ExamScores.Average()
        ).Max()
    };

// 等效
var queryGroupMax =
    students
        .GroupBy(student => student.Year)
        .Select(studentGroup => new
        {
            Level = studentGroup.Key,
            HighestScore = studentGroup.Max(student2 => student2.ExamScores.Average())
        });
```

ps： 对分组操作执行子查询： https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/perform-a-subquery-on-a-grouping-operation
