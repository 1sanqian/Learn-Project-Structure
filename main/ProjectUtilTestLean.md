# InlineData

InlineData 通常用于传递测试方法的参数，参数时测试方法的输入值
+ 使用InlineData 可以为同一个测试方法提供多组输入，确保都能被测到

```
[Theory]
[InlineData(2, 3, 5)]
[InlineData(0, 0, 0)]
[InlineData(-1, 1, 0)]
public void AddMethod_ShouldReturnCorrectSum(int a, int b, int expectedSum)
{
    // Test logic using provided input values
    // Assertion to verify the result
}
```

# ClassData

ClassData 则允许你从外部类中提供测试数据
+ 测试数据存储在另一个类中，并通 ClassData 属性传递给测试方法
+ 适用于大量测试数据或在不同场景下对同一测试方法进行测试
+ ⚠️注意： 一个   yield return new object[] { ...... }  代表一次测试

```
public class GetSmartAssistantTestCase
{
  public List<ExternalApplication> ExternalApplication { get; set; }
}

public class GetSmartAssistantDataProvider : IEnumerable<object[]>
{ public IEnumerator<object[]> GetEnumerator()
    {
        yield return new object[]
        {
            new GetSmartAssistantTestCase
            {
                ExternalApplication = new List<ExternalApplication>()
                {
                    new = ExternalApplication()
                        {
                            Id = 52,
                            Uuid = Guid.Parse("e581c3fb-8ed0-4b8f-82fc-2453b84d403e"),
                            CreatedDate = DateTimeOffset.Now,
                            Name = "CSGSgroup"
                        }
                },
            } 
       }
       yield return new object[]
        {
            new GetSmartAssistantTestCase
            {
                ExternalApplication = new List<ExternalApplication>()
                {
                    new = ExternalApplication()
                        {
                            Id = 53,
                            Uuid = Guid.Parse("f678c3fb-8ed0-4b8f-82fc-2453b84d403e"),
                            CreatedDate = DateTimeOffset.Now,
                            Name = "CSGSgroup"
                        }
                },
            } 
       }
   }
 IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}

    [Theory]
    [ClassData(typeof(GetSmartAssistantDataProvider))]
    public async Task CanSmartAssistantGetData(GetSmartAssistantTestCase testCase)
    {
     }
```

# MemberData

MemberData 也允许从外部提供测试数据
+ 不同的是，MemberData 允许使用方法而不仅仅是类来提供数据

```
public class TestDataClass
{
    public static IEnumerable<object[]> TestData()
    {
        yield return new object[] { 2, 3, 5 };
        yield return new object[] { 0, 0, 0 };
        yield return new object[] { -1, 1, 0 };
    }
}

[Theory]
[MemberData(nameof(TestDataClass.TestData), MemberType = typeof(TestDataClass))]
public void AddMethod_ShouldReturnCorrectSum(int a, int b, int expectedSum)
{
    // Test logic using provided input values
    // Assertion to verify the result
}
```

# Randomized 

允许在测试运行时动态生成随机的测试数据，以确保测试的覆盖范围更广
使用特殊的属性或者方法实现，如NUnit 的 `Random` 属性

```
[Property(Arbitrary = new[] { typeof(ArbitraryDataProvider) })]
public void MyRandomizedTest(int a, int b)
{
    // Test logic using randomly generated input values
    // Assertion to verify the result
}
```

# Environment Variables

测试参数也可以通过环境变量传递，在某些情况下可以用于配置测试环境或传递一些特殊的配置信息

```
// 尝试获取 MY_PARAM 的环境变量的值
int myParam = int.Parse(Environment.GetEnvironmentVariable("MY_PARAM"));
```

# Substitute.For<T>();

Substitute.For<T>() 表示使用 NSubstitute 框架创建一个 T 接口的替代对象

```
using NSubstitute;

public interface IOpenAiService
{
    string GetResponse(string input);
}

// 在测试中使用 NSubstitute 创建替代对象
var mockOpenAiService = Substitute.For<IOpenAiService>();

// 配置替代对象的行为,配置其在测试环境中一旦被调用就返回 Mocked response 值
mockOpenAiService.GetResponse("Hello").Returns("Mocked response");

// 调用测试代码，使用模拟对象
string result = mockOpenAiService.GetResponse("Hello");

// 断言或验证模拟对象的使用
Assert.AreEqual("Mocked response", result);
```


# builder 额外配置

```
wait Run<IMediator>(
    async mediator =>
    {
      // 方法
    builder =>
    {
        // 你可以在这里配置额外的服务或设置
        // 使用提供的构建器
        // 可以在这里Substitute.For<T>(); 配置替代对象，并且配置预设定返回值
        // 更多的是在测试中使用，用于测试环境无法调用的使用的函数进行配置
    });
```
