# Test 

在 C#  中，测试是一个重要的模块，可以保障代码的正确性和可靠性。测试的许多框架如：
+ xUnit
+ Shouldly
+ Nsubstitue

而测试的范畴通常涵盖了多个层次和类型，本文主要介绍一下几种测试： 
+ 单元测试（Unit Testing）
   测试每个方法，类别和函数的单独单元，通常使用测试框架xUnit和NUnit
+ 集成测试 （Integration Testing）
   测试不同模块或服务之间的集成，确保模块或服务之间可以协同工作，通常包括数据库和其他外部依赖 
+ E2E 测试（End-to-End Testing）
    是一种软件测试方法，测试整个应用程序的完整流程，模拟真实用户的操作，通常模拟用户从应用程序的一个端到一个端的操作，涵盖了应用程序的所有层次，包括前端、后端、数据库等 

**Triple A (AAA)模式：指的是测试中的三个阶段：Arrange(准备)、Act(执行)、Assert(断言)**

这三个阶段的含义:
Arrange（安排）： 在这个阶段，你准备测试所需的所有前提条件。这可能包括创建对象、设置测试数据、配置测试环境等。目的是确保测试运行时的初始状态是已知和受控的

Act（执行）： 在这个阶段，执行要测试的操作或动作。这是触发被测代码执行的步骤。通常，这是调用一个方法或操作，对应于你想要测试的功能

Assert（断言）： 在这个阶段，检查测试结果是否符合预期。这通常包括使用断言语句，确保实际结果与预期结果一致。如果断言失败，则测试被标记为失败

## 测试层次

### 1. 单元测试

 单元测试的基本工作流程：
+ 选择单元：选择要测试的最小单元，通常是单一方法，函数或类别
+ 编写测试： 为所选单元编写测试，确保测试是小型，独立，可重复的
+ 执行测试： 使用测试框架执行单元测试
+ 检查结果： 检查测试运行的结果，确保每个测试都能通过

例：

```
// 选择一个简单的 Calculator 类，其中 Add 方法进行测试
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }
}

// 对应的单元测试可以是：
[TestFixture]
public class CalculatorTests
{
    [Test]
    public void Add_ShouldReturnSum()
    {
        // Arrange 准备： 准备测试所需要的对象实例、数据和环境，包括曾经类的实例、设置输入参数和初始化测试数据
        var calculator = new Calculator();

        // Act 执行：执行测试操作或调用要测试的方法
        var result = calculator.Add(2, 3);

        // Assert 断言：检验测试的结果是否符合预期
        Assert.AreEqual(5, result);
    }
}
```

### 2. 集成测试

 集成测试的基本工作流程：
+ 选择整合点：选择要进行整合测试的模块、服务组件
+ 编写测试：编写测试以确保选择的整合点能夠正确协同工作 
+ 执行测试：执行整合测试，测试整合点的集成
+ 检查结果：检查测试结果，确保整合点的协同工作正确

例：

```
// 接口定义
public interface IPaymentGateway
{
    bool ProcessPayment(decimal amount);
}

// 实际的支付网关实现
public class RealPaymentGateway : IPaymentGateway
{
    public bool ProcessPayment(decimal amount)
    {
        // 实际的支付处理逻辑
        // 在真实环境中，这里可能会调用外部支付服务
        return true; // 模拟支付成功
    }
}

// 订单类
public class Order
{
    public decimal TotalAmount { get; set; }
}

// 选择一个简单的 OrderProcessor 类，其中整合了 PaymentGateway 服务
public class OrderProcessor
{
    private readonly IPaymentGateway _paymentGateway;

    public OrderProcessor(IPaymentGateway paymentGateway)
    {
        _paymentGateway = paymentGateway;
    }

    public bool ProcessOrder(Order order)
    {
        // 一些订单处理逻辑

        // 调用支付网关
        return _paymentGateway.ProcessPayment(order.TotalAmount);
    }
}

// 对应的集成测试可以是：
// 集成测试
[TestFixture]
public class OrderProcessorIntegrationTests
{
    [Test]
    public void ProcessOrder_ShouldProcessPayment()
    {
        // Arrange
        var paymentGateway = new MockPaymentGateway();
        var orderProcessor = new OrderProcessor(paymentGateway);
        var order = new Order { TotalAmount = 100 };

        // Act
        var result = orderProcessor.ProcessOrder(order);

        // Assert
        Assert.IsTrue(result);
        Assert.IsTrue(paymentGateway.PaymentProcessed);
    }

    // 模拟支付网关
    public class MockPaymentGateway : IPaymentGateway
    {
        public bool PaymentProcessed { get; private set; }

        public bool ProcessPayment(decimal amount)
        {
            // 模拟支付处理逻辑
            PaymentProcessed = true;
            return true; // 模拟支付成功
        }
    }
}
```

### 3. End-to-End 测试

 End-to-End 测试的基本工作流程：
+ 选择场景：选择一个或者多个用户场景，包含应用程序的整个流程
+ 编写测试：编写模拟用户行为的测试，包括点击按钮、填写表单、导航页面等
+ 执行测试：使用E2E测试框架（Selenium、Cypress）执行测试
+ 检查结果：检查测试结果，确保整个应用程序的流程正确运作

例：

```
using NUnit.Framework;
using OpenQA.Selenium;
using OpenQA.Selenium.Chrome;

[TestFixture]
public class ShoppingCartE2ETests
{
    private IWebDriver _driver;

    [SetUp]
    public void Setup()
    {
        // 初始化 WebDriver
        _driver = new ChromeDriver();
    }

    [TearDown]
    public void TearDown()
    {
        // 关闭 WebDriver
        _driver.Quit();
    }

    [Test]
    public void CompleteShoppingProcess_ShouldCheckoutSuccessfully()
    {
        // Arrange
        var shoppingPage = new ShoppingPage(_driver);

        // Act
        shoppingPage.Open();
        shoppingPage.Login("username", "password");
        shoppingPage.SelectProduct("ProductA");
        shoppingPage.AddToCart();
        shoppingPage.Checkout();

        // Assert
        Assert.IsTrue(shoppingPage.IsOrderPlacedSuccessfully);
    }
}

// 页面对象模式，用于封装页面上的元素和操作
public class ShoppingPage
{
    private readonly IWebDriver _driver;

    public ShoppingPage(IWebDriver driver)
    {
        _driver = driver;
    }

    public void Open()
    {
        _driver.Navigate().GoToUrl("http://example.com/shopping");
    }

    public void Login(string username, string password)
    {
        // 实现登录逻辑，填写用户名和密码
    }

    public void SelectProduct(string productName)
    {
        // 选择商品的逻辑
    }

    public void AddToCart()
    {
        // 将商品添加到购物车的逻辑
    }

    public void Checkout()
    {
        // 结账的逻辑
    }

    public bool IsOrderPlacedSuccessfully
    {
        get
        {
            // 检查订单是否成功放置的逻辑，返回 true 或 false
            return true;
        }
    }
}
```

## 测试框架

### xUnit

xUnit 是一个用于编写单元测试的开源测试框架，它支持多种编程语言，包括 .NET 平台上的 C#。xUnit 的设计理念包括简洁、灵活和易于扩展

xUnit.net 支持两种不同主要类型的单元测试：事实 Fact 和理论 Theory:
+ 事实Fact是永远正确的测试。他们测试不变条件
+ 理论Theory是仅适用于一组特定数据的测试

以下是一个 xUnit 的一个demo：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-e6031e510fd4737a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-2d539faee075ccec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-5cf84f35585ad802.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

值得注意的是：理论Theory测试 虽然只写了个3个，但是实际上测试运行器运行了6个测试，因为每个理论及其数据集都是一个单独的测试；另请注意，运行程序会准确告诉您哪组数据失败，因为它显示参数值

ps: xUnit 框架的官方网站: https://xunit.net/

### Shouldly

Shouldly 是一个用于编写更富表达力的断言的 .NET 测试框架，它基于 xUnit、NUnit 和 MSTest。Shouldly 提供了一种更自然语言的断言方式，它的重点就是在断言失败时提供重要的错误信息，同时又简单又简洁

旧的断言方式：

```
Assert.That(contestant.Points, Is.EqualTo(1337));
```

失败时会收到以下消息：

```
Expected 1337 but was 0
```

应当：

```
contestant.Points.ShouldBe(1337);
```

还有一个并排的例子：

```
 Assert.That(map.IndexOfValue("boo"), Is.EqualTo(2));
// -> Expected 2 but was -1

map.IndexOfValue("boo").ShouldBe(2);
// -> map.IndexOfValue("boo") should be 2 but was -1
```
以下是一些Shouldly 中常用的关键句：
#### 1. ShouldBe

用于比较实际值和期望值是否相等

```
actualValue.ShouldBe(expectedValue);
```

#### 2. ShouldNotBe

用于比较实际值和期望值是否不相等

```
actualValue.ShouldNotBe(notExpectedValue);
```

#### 3. ShouldBeNull

用于验证实际值是否为 null

```
actualValue.ShouldBeNull();
```

#### 4. ShouldNotBeNull

用于验证实际值是否不为 null

```
actualValue.ShouldNotBeNull();
```

#### 5. ShouldBeTrue

用于验证条件是否为真

```
condition.ShouldBeTrue();
```

#### 6. ShouldBeFalse

用于验证条件是否为假

```
condition.ShouldBeFalse();
```

#### 7. ShouldBeGreaterThan

用于验证实际值是否大于期望值

```
actualValue.ShouldBeGreaterThan(expectedValue);
```

#### 8. ShouldBeGreaterThanOrEqualTo

用于验证实际值是否大于或等于期望值

```
actualValue.ShouldBeGreaterThanOrEqualTo(expectedValue);
```

#### 9. ShouldBeLessThan

用于验证实际值是否小于期望值

```
actualValue.ShouldBeLessThan(expectedValue);
```

#### 10. ShouldBeLessThanOrEqualTo

用于验证实际值是否小于或等于期望值

```
actualValue.ShouldBeLessThanOrEqualTo(expectedValue);
```

#### 11. ShouldContain

用于验证集合是否包含特定元素

```
collection.ShouldContain(element);
```

#### 12. ShouldNotContain

用于验证集合是否不包含特定元素

```
collection.ShouldNotContain(element);
```

#### 13. ShouldBeInRange

用于验证实际值是否在指定的范围内

```
actualValue.ShouldBeInRange(minimum, maximum);
```

#### 14. ShouldStartWith

用于验证字符串是否以指定的前缀开头

```
stringValue.ShouldStartWith(prefix);
```

#### 15. ShouldEndWith
用于验证字符串是否以指定的后缀结尾

```
stringValue.ShouldEndWith(suffix);
```

ps: Shouldly 框架的官方网站: https://shouldly.github.io/

### Nsubstitue

NSubstitute 是一个 .NET 平台上的动态代理框架，用于生成和配置代理对象以进行单元测试。它允许替代（substitute）实际对象，模拟方法调用，并设置期望的行为，以便进行更容易的单元测试

以下是 NSubstitute 中的一些重点特点和用法：

#### 1. 动态代理 

NSubstitute 使用动态代理技术创建代理对象。这允许创建模拟对象，而无需实际的实现

#### 2. 直观的API

NSubstitute 提供了一个直观和简洁的 API，使得创建代理对象、设置期望和验证方法调用变得容易

#### 3. Arrange-Act-Assert 模式

NSubstitute 鼓励使用 "Arrange-Act-Assert" 模式，其中 "Arrange" 阶段用于设置测试的前置条件，"Act" 阶段执行测试操作，"Assert" 阶段用于验证期望的行为

#### 4. 配置代理对象的行为

通过 NSubstitute，你可以配置代理对象的方法以模拟它们的行为。例如，你可以指定方法的返回值，设置抛出异常等 

**代理对象类型**：`Substitute.For<T>()`  返回的对象类型是 T 的代理对象，可以是接口、抽象类或者虚方法的类

```
var calculator = Substitute.For<ICalculator>();
```

**默认行为**：代理对象上的所有方法都是默认行为，通常返回默认值（null、0、false 等）`substitute.SomeMethod().Returns("MockedValue")`。你可以手动配置方法的返回值，以模拟特定的行为 

```
// 配置方法的返回值
calculator.Add(1, 2).Returns(3);
```

**方法的模拟**： 你可以使用代理对象模拟接口或者虚方法的行为`substitute.Received().SomeMethod()`，比如配置方法的返回值、设置对方法的调用进行验证等。

```
// 验证方法的调用: 验证方法calculator.Add()是否被调用例一次
calculator.Received(1).Add(1, 2);
```

#### 5. 参数匹配

NSubstitute 支持使用参数匹配进行更灵活的配置。你可以使用 `Arg.Is`、`Arg.Any` 等方法来匹配特定的参数值

```
// 示例：使用参数匹配
calculator.Add(Arg.Is<int>(x => x > 0), Arg.Any<int>()).Returns(999);
```

#### 6.验证方法调用

使用 NSubstitute，你可以验证代理对象的方法是否按预期被调用。例如，你可以验证方法被调用了指定的次数

```
// 示例：验证方法调用
calculator.Received(1).Add(1, 2);
```

#### 7.清除代理对象的配置

NSubstitute 允许你清除代理对象的配置，以便在测试中重新配置它们。这对于在测试过程中更改代理对象的行为很有用

```
// 示例：清除代理对象的配置
calculator.ClearReceivedCalls();
```

#### 8. 自动属性替代

NSubstitute 支持自动属性替代，可以创建并配置带有 getter 和 setter 的属性

```
// 示例：自动属性替代
var person = Substitute.For<IPerson>();
person.Name.Returns("John Doe");
```

NSubstitute 的一个例子：

```
using NSubstitute;
using Xunit;

public interface ICalculator
{
    int Add(int a, int b);
}

public class MathService
{
    private readonly ICalculator _calculator;

    public MathService(ICalculator calculator)
    {
        _calculator = calculator;
    }

    public int AddNumbers(int x, int y)
    {
        // 调用依赖的 Calculator 接口方法
        return _calculator.Add(x, y);
    }
}

public class MathServiceTests
{
    [Fact]
    public void AddNumbers_ShouldCallAddMethodOnCalculator()
    {
        // Arrange
        var calculator = Substitute.For<ICalculator>();
        var mathService = new MathService(calculator);

        // Act
        mathService.AddNumbers(2, 3);

        // Assert
        // 验证 Calculator 接口的 Add 方法被调用
        calculator.Received(1).Add(2, 3);
    }
}
```

ps: NSubstitute 框架的官方网站: https://nsubstitute.github.io/
