# 一、 枚举

枚举类型 是由基础[整型数值类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/value-types)的一组命名常量定义的[值类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/integral-numeric-types)

枚举类型是使用 enum 关键字声明的，定义枚举的名称、可访问性、基础类型和成员

c# 的枚举是值类型，即枚举包含自己的值，且不能继承或传递继承

#### 1. 声明枚举的一般语法

```
// enum_name: 枚举的类型名称 
// enumeration list: 是一个用逗号分隔的标识符列表
enum <enum_name> 
{ 
      enumeration list
};
``` 

+ 每个枚举类型都有一个相应的整型，称为枚举类型的基础类型，默认情况下，未显式声明基础类型的枚举声明的基础类型为int
+ 从第一个枚举符号的值 0开始，并按定义文本顺序递增 1
+ 枚举基础类型应能够表示枚举中定义的所有枚举值，若enum_base存在，则它显式声明基础类型
+ 枚举类型 E 的默认值是由表达式 (E)0 生成的值，即使零没有相应的枚举成员也是如此
+ 枚举修饰符一般有 `new`， `public`， `protected`， `internal `， `private`
+ 枚举声明中不允许使用 abstract 和 sealed 修饰符，static 枚举不能是抽象也不允许派生
+ 枚举类型声明的主体定义零个或多个枚举成员，它们是枚举类型的命名常量，两个枚举成员不能具有相同的名称

**多个枚举成员可以共享相同的关联值**

```
enum Color
{
    Red,
    Green,
    Blue,
    Max = Blue
//显示一个枚举，其中两个枚举成员 -Blue和Max- 具有相同的关联值
}
```

**指定 其他整数数值类型 作为 枚举类型 的 基本类型**

```
enum ErrorCode : ushort
{
    None = 0,
    Unknown = 1,
    ConnectionLost = 100,
    OutlierReading = 200
}
```

不能在枚举类型的定义内定义方法，若要向枚举类型添加新概念，要创建[扩展方法](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)

可以通过使用枚举类型，用一组互斥值 或者 选项组合 来表示 选项
但是如果要表示选项组合，则要将枚举类型定义为 位标志

#### 2. 作为位标志的枚举类型

用枚举类型表示选项组合，则要为这些选项定义枚举成员，以便单个选项成为位字段，即 枚举成员的关联值必须是 2 的幂次方，且可以使用 [按位逻辑运算符`|`或 `&`](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#enumeration-logical-operators) 分别合并选项或交叉组合选项

若要指示枚举类型声明位字段，请对其应用 [Flags](https://learn.microsoft.com/zh-cn/dotnet/api/system.flagsattribute) 属性

示例

```
[Flags]
public enum Days
{
    None      = 0b_0000_0000,  // 0
    Monday    = 0b_0000_0001,  // 1
    Tuesday   = 0b_0000_0010,  // 2
    Wednesday = 0b_0000_0100,  // 4
    Thursday  = 0b_0000_1000,  // 8
    Friday    = 0b_0001_0000,  // 16
    Saturday  = 0b_0010_0000,  // 32
    Sunday    = 0b_0100_0000,  // 64
    Weekend   = Saturday | Sunday
}

public class FlagsEnumExample
{
    public static void Main()
    {
        // 枚举成员 可以用 按位逻辑运算符 进行组合
        Days meetingDays = Days.Monday | Days.Wednesday | Days.Friday;
        Console.WriteLine(meetingDays);
        // Output:
        // Monday, Wednesday, Friday

        Days workingFromHomeDays = Days.Thursday | Days.Friday;
        Console.WriteLine($"Join a meeting by phone on {meetingDays & workingFromHomeDays}");
        // Output:
        // Join a meeting by phone on Friday

        bool isMeetingOnTuesday = (meetingDays & Days.Tuesday) == Days.Tuesday;
        Console.WriteLine($"Is there a meeting on Tuesday: {isMeetingOnTuesday}");
        // Output:
        // Is there a meeting on Tuesday: False

        var a = (Days)37;
        Console.WriteLine(a);
        // Output:
        // Monday, Wednesday, Saturday
    }
}
```

以下运算符可用于枚举类型的值：

*   `==`, `!=`, `<`, `>`, `<=`, `>=`( [§12.12.6](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#12126-enumeration-comparison-operators) )
*   二进制`+`（[§12.10.5](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#12105-addition-operator)）
*   二进制`-`（[§12.10.6](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#12106-subtraction-operator)）
*   `^`, `&`, `|`( [§12.13.3](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#12133-enumeration-logical-operators) )
*   `~`（[第 12.9.5 条](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#1295-bitwise-complement-operator)）
*   `++`，`--`（[第 12.8.15 条](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#12815-postfix-increment-and-decrement-operators)和[第 12.9.6 条](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/expressions#1296-prefix-increment-and-decrement-operators)）
*   `sizeof`（[第 23.6.9 条](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/unsafe-code#2369-the-sizeof-operator)）



#### 3. 枚举约束

[System.Enum](https://learn.microsoft.com/zh-cn/dotnet/api/system.enum) 类型是所有枚举类型的抽象基类，并且继承自的成员System.Enum在任何枚举类型中都可用，它提供多种方法来获取有关枚举类型及其值的信息

可在基类约束中使用 `System.Enum`（称为[枚举约束](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters#enum-constraints)），以指定类型参数为枚举类型。 所有枚举类型也都满足 `struct` 约束，此约束用于指定类型参数为不可为 null 的值类型


#### 4. 枚举类型转换

对于任何枚举类型，枚举类型与其基础整型类型之间存在显式转换。 如果将枚举值[转换](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-expression)为其基础类型，则结果为枚举成员的关联整数值

```
public enum Season
{
    Spring,
    Summer,
    Autumn,
    Winter
}

public class EnumConversionExample
{
    public static void Main()
    {
        Season a = Season.Autumn;
        Console.WriteLine($"Integral value of {a} is {(int)a}");  // output: Integral value of Autumn is 2

        var b = (Season)1;
        Console.WriteLine(b);  // output: Summer

        var c = (Season)4;      // 虽然 (Season)4没有相应的枚举成员 但相应的还是可以枚举出 关联的整数值
        Console.WriteLine(c);  // output: 4
    }
}
```

使用 [Enum.IsDefined](https://learn.microsoft.com/zh-cn/dotnet/api/system.enum.isdefined) 方法来确定枚举类型是否包含具有特定关联值的枚举成员

对于任何枚举类型，都存在分别与 [System.Enum](https://learn.microsoft.com/zh-cn/dotnet/api/system.enum) 类型的[装箱和取消装箱](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/types/boxing-and-unboxing)相互转换

ps: 

枚举： https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/language-specification/enums

枚举类型： https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/enum
