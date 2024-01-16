# 一、Lambda

#### 1. Lambda 表达式
Lambda 表达式是 C# 中一种简洁的匿名函数形式，它允许我们在需要函数作为参数的地方提供一种更简洁的语法

Lambda 表达式通常用于函数式编程和 LINQ 查询

表达式位于 => 运算符右侧的 Lambda 表达式称为“表达式 Lambda”, Lambda 表达式的一般语法如下

```
(parameters) => expression
```


+ parameters 是 Lambda 表达式的参数列表，可以是零个或多个参数。每个参数可以有类型声明，也可以根据上下文推断类型
+ => 是 Lambda 表达式的箭头符号，表示参数到表达式的映射
+ expression 是 Lambda 表达式的主体，即要执行的代码逻辑

任何 Lambda 表达式都可以转换为[委托](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/reference-types#the-delegate-type)类型

 Lambda 表达式可以转换的委托类型由其参数和返回值的类型定义

 如果 Lambda 表达式不返回值，则可以将其转换为 Action 委托类型之一；否则，可将其转换为 Func 委托类型之一

 例如，有 2 个参数且不返回值的 Lambda 表达式可转换为 [Action<T1,T2>](https://learn.microsoft.com/zh-cn/dotnet/api/system.action-2) 委托。 有 1 个参数且不返回值的 Lambda 表达式可转换为 [Func<T,TResult>](https://learn.microsoft.com/zh-cn/dotnet/api/system.func-2) 委托

以下有几个例子是用 lambda 表达式 x => x * x 将分配给委托类型的变量

```
// 1.
Func<int, int> square = x => x * x;
Console.WriteLine(square(5));
// Output:
// 25 

// 2. 表达式 lambda 还可以转换为 表达式树
System.Linq.Expressions.Expression<Func<int, int>> e = x => x * x;
Console.WriteLine(e);
// Output:
// x => (x * x)

//3.  用 C# 编写 LINQ时，还可以使用 lambda 表达式
int[] numbers = { 2, 3, 4, 5 };
var squaredNumbers = numbers.Select(x => x * x);
Console.WriteLine(string.Join(" ", squaredNumbers));
// Output:
// 4 9 16 25
```

####  2. lambda 表达式的输入参数

+ 将 lambda 表达式的输入参数括在括号中。 使用空括号指定零个输入参数

```
Action line = () => Console.WriteLine();
```

+ 如果 lambda 表达式只有一个输入参数，则括号是可选的

```
Func<double, double> cube = x => x * x * x;
```

+ 两个或更多输入参数使用逗号加以分隔

```
Func<int, int, bool> testForEquality = (x, y) => x == y;
```

+ 有时，编译器无法推断输入参数的类型。 可以显式指定类型，如下面的示例所示

```
Func<int, string, bool> isTooLong = (int x, string s) => s.Length > x;
```

**注意**：输入参数类型必须全部为显式或全部为隐式；否则，便会生成 [CS0748](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/compiler-messages/lambda-expression-errors#lambda-expression-parameters-and-returns) 编译器错误

+ 可以使用[弃元](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/functional/discards)来指定 Lambda 表达式中未使用的两个或更多输入参数

```
Func<int, int, int> constant = (_, _) => 42;


// 弃元是一个占位符变量，用来指定 Lambda 表达式中 未使用的输入参数，在使用 lambda 表达式时，弃元的参数一般是被忽视不使用的，以下是弃元的例子
var (_, _, _, pop1, _, pop2) = QueryCityDataForYears("New York City", 1960, 2010);

//表达式 "{pop2 - pop1:N0}" 是一个格式化字符串，用于在数学或统计上表示 "pop2" 减去 "pop1" 的差值，并将结果格式化为带有千位分隔符的整数
Console.WriteLine($"Population change, 1960 to 2010: {pop2 - pop1:N0}");   // ":N0" 是一个格式化选项，指示将结果格式化为带有千位分隔符的整数，即每三位数字用逗号隔开。

static (string, double, int, int, int, int) QueryCityDataForYears(string name, int year1, int year2)
{
    int population1 = 0, population2 = 0;
    double area = 0;

    if (name == "New York City")
    {
        area = 468.48;
        if (year1 == 1960)
        {
            population1 = 7781984;
        }
        if (year2 == 2010)
        {
            population2 = 8175133;
        }
        return (name, area, year1, population1, year2, population2);
    }

    return ("", 0, 0, 0, 0, 0);
}
// The example displays the following output:
// Population change, 1960 to 2010: 393,149
```

使用 lambda 表达式[提供事件处理程序](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/events/how-to-subscribe-to-and-unsubscribe-from-events)时，lambda 弃元参数可能很有用

 *备注： 为了向后兼容，如果只有一个输入参数命名为 `_`，则在 lambda 表达式中，`_` 将被视为该参数的名称*

+ lambda 表达式上的参数提供默认值，默认参数值的语法和限制与方法和局部函数相同

```
var IncrementBy = (int source, int increment = 1) => source + increment;
Console.WriteLine(IncrementBy(5)); // 6
Console.WriteLine(IncrementBy(5, 2)); // 7
```

+ 使用 params 数组作为参数声明 lambda 表达式

```
var sum = (params int[] values) =>
{
    int sum = 0;
    foreach (var value in values) 
        sum += value;
    
    return sum;
};

var empty = sum();
Console.WriteLine(empty); // 0

var sequence = new[] { 1, 2, 3, 4, 5 };
var total = sum(sequence); // 0+1+2+3+4+5 = 15
Console.WriteLine(total); // 15
```

#### 3. 语句 lambda

语句 lambda 与表达式 lambda 类似，只是语句括在大括号中

```
(input-parameters) => { <sequence-of-statements> }
```

语句 lambda 的主体可以包含任意数量的语句；但是，实际上通常不会多于两个或三个

```
Action<string> greet = name =>
{
    string greeting = $"Hello {name}!";
    Console.WriteLine(greeting);
};
greet("World");
// Output:
// Hello World!
```

**注意** ： 不能使用语句 Lambda 创建表达式树

#### 4. 异步lambda

通过使用 [async](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/async) 和 [await](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/await) 关键字，可以轻松创建包含异步处理的 lambda 表达式和语句

以下是没有调用 lambda 表达式和语句 的事件处理程序，调用和等待异步方法 ExampleMethodAsync

```
public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
        button1.Click += button1_Click;
    }

    private async void button1_Click(object sender, EventArgs e)
    {
        await ExampleMethodAsync();
        textBox1.Text += "\r\nControl returned to Click event handler.\n";
    }

    private async Task ExampleMethodAsync()
    {
        // The following line simulates a task-returning asynchronous process.
        await Task.Delay(1000);
    }
}
```

若使用异步 lambda 添加同一事件处理程序，可以在 lambda参数列表前添加 async 修饰符

```
public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
        button1.Click += async (sender, e) =>
        {
            await ExampleMethodAsync();
            textBox1.Text += "\r\nControl returned to Click event handler.\n";
        };
    }

    private async Task ExampleMethodAsync()
    {
        // The following line simulates a task-returning asynchronous process.
        await Task.Delay(1000);
    }
}
```

#### 5. lambda 表达式和元组

C# 语言提供对元组的内置支持。可以提供一个元组作为 lambda 表达式的参数，同时 lambda 表达式 也可以返回元组，在默写情况下，C#编译器 使用类型推理来确定元组组件的类型

+ 可 通过用括号 括住 用逗号分隔的组件列表来定义元组

```
// 用包含三个组件的元组，将一系列数字传递给 lambda 表达式
// 此表达式将每个值翻倍，然后返回包含乘法运算结果的元组（内含三个组件）
Func<(int, int, int), (int, int, int)> doubleThem = ns => (2 * ns.Item1, 2 * ns.Item2, 2 * ns.Item3);
var numbers = (2, 3, 4);
var doubledNumbers = doubleThem(numbers);
Console.WriteLine($"The set {numbers} doubled: {doubledNumbers}");

// Output:
// The set (2, 3, 4) doubled: (4, 6, 8)
```

通常，元组字段命名为 Item1、Item2 等等。 但是，可以使用命名组件定义元组

```
Func<(int n1, int n2, int n3), (int, int, int)> doubleThem = ns => (2 * ns.n1, 2 * ns.n2, 2 * ns.n3);
var numbers = (2, 3, 4);
var doubledNumbers = doubleThem(numbers);
Console.WriteLine($"The set {numbers} doubled: {doubledNumbers}");
```

*元组微软官网 ： https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/value-tuples*

#### 6. 含标准查询运算符的 lambda

在其他实现中，LINQ to Objects 有一个输入参数，其类型是泛型委托 [Func<TResult>](https://learn.microsoft.com/zh-cn/dotnet/api/system.func-1) 系列中的一种。 这些委托使用类型参数来定义输入参数的数量和类型，以及委托的返回类型。 `Func` 委托对于封装用户定义的表达式很有用，这些表达式将应用于一组源数据中的每个元素

+  [Func<T,TResult>](https://learn.microsoft.com/zh-cn/dotnet/api/system.func-2) 委托类型

```
// 可以将委托实例化为 Func<int, bool> 实例，其中 int 是输入参数，bool 是返回值
// 返回值始终在最后一个类型参数中指定
public delegate TResult Func<in T, out TResult>(T arg)

Func<int, bool> equalsFive = x => x == 5;
bool result = equalsFive(4);
Console.WriteLine(result);   // False
```

+ 使用 [Count](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.count) 标准查询运算符

```
//编译器可以推断输入参数的类型，也可以显式指定该类型。以下Count特殊 lambda 表达式将计算那些除以 2 时余数为 1 的整数的数量 (n)
int[] numbers = { 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 };
int oddNumbers = numbers.Count(n => n % 2 == 1);
Console.WriteLine($"There are {oddNumbers} odd numbers in {string.Join(" ", numbers)}");
//There are 5 odd numbers in 5 4 1 3 9 8 6 7 2 0

//生成一个序列
int[] numbers = { 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 };
var firstNumbersLessThanSix = numbers.TakeWhile(n => n < 6);
Console.WriteLine(string.Join(" ", firstNumbersLessThanSix));
// Output:
// 5 4 1 3
```

+ 通过将输入参数括在括号中来指定多个输入参数

```
//  此方法返回 numbers 数组中的所有元素，直至发现值小于其在数组中的序号位置的数字为止
int[] numbers = { 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 };
var firstSmallNumbers = numbers.TakeWhile((n, index) => n >= index);
Console.WriteLine(string.Join(" ", firstSmallNumbers));
// Output:
// 5 4
```

+ 不能直接在[查询表达式](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/query-keywords)中使用 lambda 表达式，但可以在查询表达式的方法调用中使用它们

```
var numberSets = new List<int[]>
{
    new[] { 1, 2, 3, 4, 5 },
    new[] { 0, 0, 0 },
    new[] { 9, 8 },
    new[] { 1, 0, 1, 0, 1, 0, 1, 0 }
};

var setsWithManyPositives = 
    from numberSet in numberSets
    where numberSet.Count(n => n > 0) > 3
    select numberSet;

foreach (var numberSet in setsWithManyPositives)
{
    Console.WriteLine(string.Join(" ", numberSet));
}
// Output:
// 1 2 3 4 5
// 1 0 1 0 1 0 1 0
```

#### 7. lambda 表达式中的类型推理

编写 lambda 时，通常不必为输入参数指定类型，因为编译器可以根据 lambda 主体、参数类型以及 C# 语言规范中描述的其他因素来推断类型

对于大多数标准查询运算符，第一个输入是源序列中的元素类型

 如果要查询 IEnumerable<Customer>，则输入变量将被推断为 Customer 对象，这意味着你可以访问其方法和属性

```
customers.Where(c => c.City == "London");
```

lambda 类型推理的一般规则如下：

*   Lambda 包含的参数数量必须与委托类型包含的参数数量相同
*   Lambda 中的每个输入参数必须都能够隐式转换为其对应的委托参数
*   Lambda 的返回值（如果有）必须能够隐式转换为委托的返回类型

#### 8. lambda 表达式中的自然类型

Lambda 表达式本身没有类型，因为通用类型系统没有“Lambda 表达式”这一固有概念。不过，有时以非正式的方式谈论 Lambda 表达式的“类型”会很方便

从 C# 10 开始，Lambda 表达式可能具有自然类型

+  编译器不会强制开发者为 Lambda 表达式声明委托类型（例如 Func<...> 或 Action<...>），而是根据 Lambda 表达式推断委托类型

```
// 编译器可以将 parse 推断为 Func<string, int>
var parse = (string s) => int.Parse(s);

// 编译器选择可用的 Func 或 Action 委托（如果存在合适的委托）, 否则，它将合成委托类型
object parse = (string s) => int.Parse(s);   // Func<string, int>
Delegate parse = (string s) => int.Parse(s); // Func<string, int>
```

+ 只有一个重载的方法组（即没有参数列表的方法名称）具有自然类型

```
var read = Console.Read; // Just one overload; Func<int> inferred
var write = Console.Write; // ERROR: Multiple overloads, can't choose
```

+ 如果将 Lambda 表达式分配给 [System.Linq.Expressions.LambdaExpression](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.expressions.lambdaexpression) 或 [System.Linq.Expressions.Expression](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.expressions.expression)，并且 Lambda 具有自然委托类型，则表达式的自然类型为 [System.Linq.Expressions.Expression<TDelegate>](https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.expressions.expression-1)，其中自然委托类型用作形参类型的实参

```
LambdaExpression parseExpr = (string s) => int.Parse(s); // Expression<Func<string, int>>
Expression parseExpr = (string s) => int.Parse(s);       // Expression<Func<string, int>>
```

+ 并非所有 Lambda 表达式都有自然类型

```
var parse = s => int.Parse(s); // ERROR: Not enough type info in the lambda

// 编译器无法推断 s 自然类型，必须声明类型
Func<string, int> parse = s => int.Parse(s);
```

#### 9. 显式返回类型

通常，Lambda 表达式的返回类型是显而易见的并且是推断出来的

但是对于某些表达式并不能起到作用

```
var choose = (bool b) => b ? 1 : "two"; // ERROR: Can't infer return type

//C# 10 开始，可以在输入参数前面指定 Lambda 表达式的返回类型。 指定显式返回类型时，必须将输入参数括起来
var choose = object (bool b) => b ? 1 : "two"; // Func<bool, object>
```

#### 10. 属性

将属性添加到 lambda  表达式

```
// "ProvidesNullCheck" 是一个自定义的特性，它应用于 parse 变量的匿名函数对空值的检查
Func<string?, int?> parse = [ProvidesNullCheck] (s) => (s is not null) ? int.Parser(s) : null;

// 将属性添加到输入参数或返回值
// DisallowNull：该参数不允许为null 值
// NotNullIfNotNull：如果s参数不为null，则返回值也不为 nul
var concat = ([DisallowNull] string a, [DisallowNull] string b) => a + b;
var inc = [return: NotNullIfNotNull(nameof(s))] (int? s) => s.HasValue ? s++ : null;
```

#### 捕获 lambda 表达式中的外部变量和变量范围

lambda 可以引用外部变量

外部变量是在定义 lambda 表达式的方法中或包含 lambda 表达式的类型中的范围内变量

以这种方式捕获的变量将进行存储以备在 lambda 表达式中使用，即使在其他情况下，这些变量将超出范围并进行垃圾回收。 必须明确地分配外部变量，然后才能在 lambda 表达式中使用该变量

![image.png](https://upload-images.jianshu.io/upload_images/29476859-03e19ae7b2d226c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-9e2463ec24cb3f1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-4f70eabb0be26834.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下列规则适用于 lambda 表达式中的变量范围：

*   捕获的变量将不会被作为垃圾回收，直至引用变量的委托符合垃圾回收的条件
*   在封闭方法中看不到 Lambda 表达式内引入的变量
*   Lambda 表达式无法从封闭方法中直接捕获 [in](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/method-parameters#in-parameter-modifier)、[ref](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/ref) 或 [out](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/method-parameters#out-parameter-modifier) 参数
*   lambda 表达式中的 [return](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/jump-statements#the-return-statement) 语句不会导致封闭方法返回
*   如果相应跳转语句的目标位于 Lambda 表达式块之外，Lambda 表达式不得包含 [goto](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/jump-statements#the-goto-statement)、[break](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/jump-statements#the-break-statement) 或 [continue](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/statements/jump-statements#the-continue-statement) 语句。 同样，如果目标在块内部，在 lambda 表达式块外部使用跳转语句也是错误的






















