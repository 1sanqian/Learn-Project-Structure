# Linq  基础

语言集成查询 (LINQ) 是一系列直接将查询功能集成到 C# 语言的技术统称

LINQ 最明显的“语言集成”部分就是查询表达式。 查询表达式采用声明性查询语法编写而成， 使用查询语法，可以用最少的代码对数据源执行筛选、排序和分组操作。 可使用相同的基本查询表达式模式来查询和转换 SQL 数据库、ADO .NET 数据集、XML 文档和流以及 .NET 集合中的数据

##  查询

### 查询简介

所有 LINQ 查询操作都由以下三个不同的操作组成：
+ 获取数据源
+ 创建查询
+ 执行查询

```
// The Three Parts of a LINQ Query:
// 1. Data source.
int[] numbers = [ 0, 1, 2, 3, 4, 5, 6 ];

// 2. Query creation.
// numQuery is an IEnumerable<int>
var numQuery =
    from num in numbers
    where (num % 2) == 0
    select num;

// 3. Query execution.
foreach (int num in numQuery)
{
    Console.Write("{0,1} ", num);
}
```

下图演示完整的查询操作。 在 LINQ 中，查询的执行不同于查询本身。 换句话说，你不会通过创建查询变量来检索任何数据

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d68a1d2238fd0c49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 数据源

数据源是一个数组，它支持泛型 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 接口。 这一事实意味着该数据源可以用 LINQ 进行查询。 查询在 `foreach` 语句中执行，且 `foreach` 需要 [IEnumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerable) 或 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1)。 支持 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 或派生接口（如泛型 [IQueryable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.iqueryable-1)）的类型称为可查询类型

可查询类型不需要进行修改或特殊处理就可以用作 LINQ 数据源。 如果源数据还没有作为可查询类型出现在内存中，则 LINQ 提供程序必须以此方式表示源数据

#### 查询执行

#####  延迟执行

查询变量本身只存储查询命令。 查询的实际执行将推迟到在 foreach 语句中循环访问查询变量之后进行。 此概念称为延迟执行

```
// foreach 语句也是检索查询结果的地方
// 由于查询变量本身从不保存查询结果，因此你可以重复执行它来检索更新的数据
foreach (int num in numQuery)
{
    Console.Write("{0,1} ", num);
}
```

#####  强制立即执行

对一系列源元素执行聚合函数的查询必须首先循环访问这些元素。 Count、Max、Average 和 First 就属于此类查询。 由于查询本身必须使用 foreach 来返回结果，因此这些方法在执行时不使用显式 foreach 语句。 这些查询返回单个值，而不是 IEnumerable 集合

```
var evenNumQuery =
    from num in numbers
    where (num % 2) == 0
    select num;
int evenNumCount = evenNumQuery.Count();
```

要强制立即执行任何查询并缓存其结果，可调用 [ToList](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.tolist) 或 [ToArray](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.toarray) 方法

```
List<int> numQuery2 =
    (from num in numbers
        where (num % 2) == 0
        select num).ToList();

// or like this:
// numQuery3 is still an int[]

var numQuery3 =
    (from num in numbers
        where (num % 2) == 0
        select num).ToArray();
```

此外，还可以通过在紧跟查询表达式之后的位置放置一个 foreach 循环来强制执行查询。 但是，通过调用 ToList 或 ToArray，也可以将所有数据缓存在单个集合对象中

ps: LINQ 查询简介: https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/get-started/introduction-to-linq-queries

### 查询表达式基础

#### 查询是什么

查询是一组指令，描述要从给定数据源（或源）检索的数据以及返回的数据应具有的形状和组织。 查询与它生成的结果不同

通常情况下，源数据按逻辑方式组织为相同类型的元素的序列

从应用程序的角度来看，原始源数据的特定类型和结构并不重要。 应用程序始终将源数据视为 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 或 [IQueryable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.iqueryable-1) 集合

对于此源序列，查询可能会执行三种操作之一：
+ 检索元素的子集以生成新序列，而不修改各个元素。 然后，查询可能以各种方式对返回的序列进行排序或分组

```
IEnumerable<int> highScoresQuery =
    from score in scores
    where score > 80
    orderby score descending
    select score;
```

+ 查询投影，将查询的序列类型投影到另一类型序列去

```
IEnumerable<string> highScoresQuery2 =
    from score in scores
    where score > 80
    orderby score descending
    select $"The score is {score}";
```

+ 检索有关源数据的单独值
    + 与特定条件匹配的元素数
    + 具有最大或最小值的元素
    + 与某个条件匹配的第一个元素，或指定元素集中特定值的总和

```
var highScoreCount = (
    from score in scores
    where score > 80
    select score
).Count();
```

#### 查询表达式

+ 查询表达式是以查询语法表示的查询
+ 查询表达式是一流的语言构造
+ 查询表达式必须以 [from](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/from-clause) 子句开头，且必须以 [select](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/select-clause) 或 [group](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/group-clause) 子句结尾
+ 在第一个 `from` 子句与最后一个 `select` 或 `group` 子句之间，可以包含以下这些可选子句中的一个或多个：[where](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/where-clause)、[orderby](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/orderby-clause)、[join](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/join-clause)、[let](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/let-clause)，甚至是其他 [from](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/from-clause) 子句
+ 可以使用 [into](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/into) 关键字，使 `join` 或 `group` 子句的结果可以充当相同查询表达式中的更多查询子句的源

##### 查询变量

在 LINQ 中，查询变量是存储查询而不是查询结果的任何变量。 更具体地说，查询变量始终是可枚举类型，在 `foreach` 语句或对其 [IEnumerator.MoveNext()](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerator.movenext#system-collections-ienumerator-movenext) 方法的直接调用中循环访问时会生成元素序列

```
// Data source.
int[] scores = [90, 71, 82, 93, 75, 82];

// Query Expression.
IEnumerable<int> scoreQuery = //query variable
    from score in scores //required
    where score > 80 // optional
    orderby score descending // optional
    select score; //must end with select or group

// Execute the query to produce the results
foreach (var testScore in scoreQuery)
{
    Console.WriteLine(testScore);
}

// Output: 93 90 82 82
/*
scoreQuery 是查询变量，它有时仅仅称为查询
查询变量不存储在 foreach 循环生成中的任何实际结果数据
并且当 foreach 语句执行时，查询结果不会通过查询变量 scoreQuery 返回
而是通过迭代变量 testScore 返回
 scoreQuery 变量可以在另一个 foreach 循环中进行循环访问
只要既没有修改它，也没有修改数据源，便会生成相同结果
*/
```

查询变量可以存储采用查询语法、方法语法或是两者的组合进行表示的查询

```
//Query syntax
// 查询变量:queryMajorCities
IEnumerable<City> queryMajorCities =
    from city in cities
    where city.Population > 100000
    select city;

// Method-based syntax
// 查询变量:queryMajorCities2
IEnumerable<City> queryMajorCities2 = cities.Where(c => c.Population > 100000);

// highestScore 不是查询变量
var highestScore = (
    from score in scores
    select score
).Max();

// or split the expression
IEnumerable<int> scoreQuery =
    from score in scores
    select score;

var highScore = scoreQuery.Max();
// the following returns the same result
highScore = scores.Max();

// largeCitiesList2 不是查询变量
var largeCitiesList = (
    from country in countries
    from city in country.Cities
    where city.Population > 10000
    select city
).ToList();

// or split the expression
IEnumerable<City> largeCitiesQuery =
    from country in countries
    from city in country.Cities
    where city.Population > 10000
    select city;
var largeCitiesList2 = largeCitiesQuery.ToList();
```

##### 查询变量的显式和隐式类型化

```
// 显式
IEnumerable<City> largeCitiesQuery =
    from country in countries
    from city in country.Cities
    where city.Population > 10000
    select city;
// 隐式
var queryCities =
    from city in cities
    where city.Population > 100000
    select city;
```

##### 开始查询表达式

查询表达式必须以 from 子句开头。 它指定数据源以及范围变量。 范围变量表示遍历源序列时，源序列中的每个连续元素。 范围变量基于数据源中元素的类型进行强类型化

##### 结束查询表达式

查询表达式必须以 group 子句或 select 子句结尾

+ group 子句
使用 group 子句可生成按指定键组织的组的序列。 键可以是任何数据类型

```
var queryCountryGroups =
    from country in countries
    group country by country.Name[0];
```

+ select 子句
使用 select 子句可生成所有其他类型的序列。 简单 select 子句只生成类型与数据源中包含的对象相同的对象的序列

```
IEnumerable<Country> sortedQuery =
    from country in countries
    orderby country.Area
    select country;

// select 子句可以用于将源数据转换为新类型的序列。 此转换也称为投影

 var queryNameAndPop =
    from country in countries
    select new
    {
        Name = country.Name,
        Pop = country.Population
    };
```

##### 使用 “into” 延续

可以在 select 或 group 子句中使用 into 关键字创建存储查询的临时标识符。 如果在分组或选择操作之后必须对查询执行额外查询操作，则可以使用 into 子句

```
// percentileQuery is an IEnumerable<IGrouping<int, Country>>
var percentileQuery =
    from country in countries
    let percentile = (int)country.Population / 10_000_000
    group country by percentile into countryGroup
    where countryGroup.Key >= 20
    orderby countryGroup.Key
    select countryGroup;

// grouping is an IGrouping<int, Country>
foreach (var grouping in percentileQuery)
{
    Console.WriteLine(grouping.Key);
    foreach (var country in grouping)
    {
        Console.WriteLine(country.Name + ":" + country.Population);
    }
}
```

#####  筛选、排序和联接

开头 from 子句与结尾 select 或 group 子句之间，所有其他子句（where、join、orderby、from、let）都是可选的。 任何可选子句都可以在查询正文中使用零次或多次

+ where 子句

使用 where 子句可基于一个或多个谓词表达式，从源数据中筛选出元素

```
IEnumerable<City> queryCityPop =
    from city in cities
    where city.Population is < 200000 and > 100000
    select city;
```

+ orderby 子句

使用 orderby 子句可按升序或降序对结果进行排序。 还可以指定次要排序顺序

```
IEnumerable<Country> querySortedCountries =
    from country in countries
    orderby country.Area, country.Population descending
    select country;
// ascending 关键字是可选的；如果未指定任何顺序，则它是默认排序顺序
```

+ join 子句
    + 使用 join 子句可基于每个元素中指定的键之间的相等比较，将一个数据源中的元素与另一个数据源中的元素进行关联和/或合并
    + 在 LINQ 中，联接操作是对元素属于不同类型的对象序列执行
    + 联接了两个序列之后，必须使用 select 或 group 语句指定要存储在输出序列中的元素
    + 还可以使用匿名类型将每组关联元素中的属性合并到输出序列的新类型中

```
var categoryQuery =
    from cat in categories
    join prod in products on cat equals prod.Category
    select new
    {
        Category = cat,
        Name = prod.Name
    };
// 还可以通过使用 into关键字将 `join` 操作的结果存储到临时变量中来执行分组联接
```

+ let 子句

查询子句本身可能包含查询表达式，这有时称为子查询。 每个子查询都以自己的 from 子句开头，该子句不一定指向第一个 from 子句中的相同数据源

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
```

ps： 查询表达式基础：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/get-started/query-expression-basics

### 编写linq查询

#### 查询语法

使用查询语法编写大多数查询来创建查询表达式

```
List<int> numbers = [5, 4, 1, 3, 9, 8, 6, 7, 2, 0];

// The query variables can also be implicitly typed by using var

// 通过应用包含 where 子句的条件来筛选或限制结果,返回源序列中值大于 7 或小于 3 的所有元素
IEnumerable<int> filteringQuery =
    from num in numbers
    where num is < 3 or > 7
    select num;

// 对返回的结果进行排序
IEnumerable<int> orderingQuery =
    from num in numbers
    where num is < 3 or > 7
    orderby num ascending
    select num;

// 根据某个键对结果进行分组,基于单词的第一个字母返回两个组
string[] groupingQuery = ["carrots", "cabbage", "broccoli", "beans", "barley"];
IEnumerable<IGrouping<char, string>> queryFoodGroups =
    from item in groupingQuery
    group item by item[0];
```

**在 [foreach](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/iteration-statements#the-foreach-statement) 语句 或 其他语句中循环 访问查询变量 之前，查询不会实际执行**

#### 方法语法

某些查询操作必须表示为方法调用。 最常见的此类方法是可返回单一数值的方法，例如 [Sum](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.sum)、[Max](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.max)、[Min](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.min)、[Average](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.average) 等。 这些方法在任何查询中都必须始终最后一个调用，因为它们返回单个值，不能用作额外查询操作的源

```
List<int> numbers1 = [5, 4, 1, 3, 9, 8, 6, 7, 2, 0];
List<int> numbers2 = [15, 14, 11, 13, 19, 18, 16, 17, 12, 10];

// Average
double average = numbers1.Average();

// Concat
IEnumerable<int> concatenationQuery = numbers1.Concat(numbers2);
```

#### 混合查询和方法语法

只需将查询表达式括在括号中，然后应用点运算符并调用方法

```
// Query #7.

// Using a query expression with method syntax
var numCount1 = (
    from num in numbers1
    where num is > 3 and < 7
    select num
).Count();

// Better: Create a new variable to store
// the method call result
IEnumerable<int> numbersQuery =
    from num in numbers1
    where num is > 3 and < 7
    select num;

var numCount2 = numbersQuery.Count();
```

ps: 编写 LINQ 查询：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/get-started/write-linq-queries

### linq查询操作中的类型关系

LINQ 查询操作在数据源、查询本身及查询执行中是强类型的，查询中变量的类型必须与数据源中元素的类型和 foreach 语句中迭代变量的类型兼容。 此强类型保证在编译时捕获类型错误，以便可以在用户遇到这些错误之前更正它们

#### 不转换源数据的查询

下图演示不对数据执行转换的 LINQ to Objects 查询操作。 源包含一个字符串序列，查询输出也是一个字符串序列
1. 数据源的类型参数决定范围变量的类型
2. 所选对象的类型决定查询变量的类型。 此处的 name 是一个字符串。 因此，查询变量是一个 IEnumerable<string>
3. 在 foreach 语句中循环访问查询变量。 因为查询变量是一个字符串序列，所以迭代变量也是一个字符串

![image.png](https://upload-images.jianshu.io/upload_images/29476859-8ae9c5a1c2ddea0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 转换源数据的查询

下图演示对数据执行简单转换的 LINQ to SQL 查询操作。 查询将一个 Customer 对象序列用作输入，并只选择结果中的 Name 属性。 因为 Name 是一个字符串，所以查询生成一个字符串序列作为输出
1. 数据源的类型参数决定范围变量的类型
2. select 语句返回 Name 属性，而非完整的 Customer 对象。 因为 Name 是一个字符串，所以 custNameQuery 的类型参数是 string，而非 Customer
3. 因为 custNameQuery 是一个字符串序列，所以 foreach 循环的迭代变量也必须是 string

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d06a5300a1cf7a91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下图演示稍微复杂的转换。 select 语句返回只捕获原始 Customer 对象的两个成员的匿名类型
1. 数据源的类型参数始终为查询中范围变量的类型
2. 因为 select 语句生成匿名类型，所以必须使用 var 隐式类型化查询变量
3. 因为查询变量的类型是隐式的，所以 foreach 循环中的迭代变量也必须是隐式的

![image.png](https://upload-images.jianshu.io/upload_images/29476859-f0b7c4a4927e9766.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 让编译器推断类型信息

编译器为查询操作中的各个变量提供强类型

![image.png](https://upload-images.jianshu.io/upload_images/29476859-83c87a2c4b47b793.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### LINQ 和泛型类型 (C#)

LINQ 查询基于泛型类型。 无需深入了解泛型即可开始编写查询。 但是，可能需要了解 2 个基本概念：

1.  创建泛型集合类（如 [List<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1)）的实例时，需将“T”替换为列表将包含的对象类型。 例如，字符串列表表示为 `List<string>`，`Customer` 对象列表表示为 `List<Customer>`。 泛型列表属于强类型，与将其元素存储为 [Object](https://learn.microsoft.com/zh-cn/dotnet/api/system.object) 的集合相比，泛型列表具备更多优势。 如果尝试将 `Customer` 添加到 `List<string>`，则会在编译时收到错误。 泛型集合易于使用的原因是不必执行运行时类型转换

2.  [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 是一个接口，通过该接口，可以使用 `foreach` 语句来枚举泛型集合类。 泛型集合类支持 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1)，正如非泛型集合类（如 [ArrayList](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.arraylist)）支持 [IEnumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerable)

#### LINQ 查询中的 IEnumerable<T> 变量

LINQ 查询变量被类型化为 [IEnumerable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1) 或派生类型（如 [IQueryable<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.iqueryable-1)）。 看到类型化为 `IEnumerable<Customer>` 的查询变量时，这只意味着执行查询时，该查询将生成包含零个或多个 `Customer` 对象的序列

```
IEnumerable<Customer> customerQuery =
    from cust in customers
    where cust.City == "London"
    select cust;

foreach (Customer customer in customerQuery)
{
    Console.WriteLine($"{customer.LastName}, {customer.FirstName}");
}
```

ps: LINQ 查询操作中的类型关系：https://learn.microsoft.com/zh-cn/dotnet/csharp/linq/get-started/type-relationships-in-linq-query-operations
