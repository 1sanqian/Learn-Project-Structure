# IoC 和 AutoFac

### 一.IoC 概述

IoC全称 Inversion of Control ，名为 控制反转，是面向对象编程的一种设计原则，用来减少计算机代码之间的耦合度。本质上是把对象创建和对象之间的调用关系交由
第三方（即IoC容器） 来管理

1. 传统的软件系统中耦合的对象关系如下图

![image.png](https://upload-images.jianshu.io/upload_images/29476859-84104e761f59cc74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


软件中的对象就像齿轮一样，协同工作，但是互相耦合，一个零件不能正常工作，整个系统就崩溃了，这是一个强耦合的系统

对象之间耦合度过高的系统，必然会出现牵一发而动全身的情形

为了解决对象间耦合度过高的问题，提出了IoC理论，用来实现对象之间的“解耦”

2. IoC容器本质上就是作为“第三方”实现具有依赖关系的对象之间的解耦

![image.png](https://upload-images.jianshu.io/upload_images/29476859-69824910cf71cfaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图所示，由于引进了中间位置的“第三方”，也就是IoC容器， 使得A、B、C、D这4个对象没有了耦合关系，齿轮之间的传动全部依靠“第三方”了，全部对象的控制权全部上缴给“第三方”IoC容器，所以，IoC容器成了整个系统的关键核心，它起到了一种类似“粘合剂”的作用，把系统中的所有对象粘合在一起发挥作用，如果没有这个“粘合剂”，对象与对象之间会彼此失去联系


由于对象创建是交由 第三方 管理，则开发者需要 向 第三方 描述清楚所需对象的特征，这一操作称为 依赖注入，依赖注入本质上就是将实例变量传入到一个对象中去

当谈到依赖注入和IoC时，常常会提到一个经典的例子是汽车（Car）和引擎（Engine）之间的关系。

在传统的编码方式中，汽车对象通常会直接创建并依赖于一个具体的引擎对象。这种情况下，汽车类需要直接实例化并控制引擎对象的创建和生命周期。

``` 
public class Car
{
    private Engine engine;

    public Car()
    {
        engine = new Engine(); // 直接实例化引擎对象
    }

    public void Start()
    {
        engine.Start();
    }
}
```

上述代码中，Car 类直接创建了一个 Engine 对象，并在需要的时候调用其 Start 方法。

然而，使用IoC和依赖注入的方式，我们可以将引擎对象的创建和管理交给外部的IoC容器来处理，将依赖关系解耦。

首先，我们需要定义一个接口来表示引擎（Engine）的功能：

```
public interface IEngine
{
    void Start();
}
```
     
然后，我们实现一个具体的引擎类，实现该接口：

```
public class Engine : IEngine
{
    public void Start()
    {
        Console.WriteLine("Engine started.");
    }
}
```
       
接下来，在汽车类中通过构造函数注入依赖的引擎对象：

```
 public class Car
{
    private IEngine engine;
    
    public Car(IEngine engine)
    {
        this.engine = engine; // 通过构造函数注入引擎对象
    }
    
    public void Start()
    {
        engine.Start();
    }
}
```
       
现在，汽车类（Car）并不直接依赖于具体的引擎类（Engine），而是依赖于一个引擎接口（IEngine）。这样，在创建汽车对象时，我们可以通过IoC容器来自动解析并注入具体的引擎实例。

例如，在使用Autofac作为IoC容器的情况下，我们可以进行如下配置：

```
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterType<Engine>().As<IEngine>(); // 注册引擎类型
    containerBuilder.RegisterType<Car>(); // 注册汽车类型
        
    var container = containerBuilder.Build(); // 构建容器
        
    var car = container.Resolve<Car>(); // 从容器中解析出汽车对象
    car.Start(); // 调用汽车的启动方法
```
        
通过上述配置和解析步骤，Autofac会自动创建一个Car对象，并将一个具体的Engine对象注入到Car对象的构造函数中。这样，Car对象就能够使用注入的引擎对象进行启动操作。

这个例子展示了使用IoC和依赖注入的方式，将对象之间的依赖关系解耦，提高了代码的可测试性和可扩展性。通过IoC容器的帮助，我们不需要手动创建和管理对象的依赖关系，而是将这个任务交给容器来完成。

### 二. 依赖注入

在 C# 中，实现 IoC 的常用方式是使用依赖注入容器（Dependency Injection Container)

依赖注入容器是一个负责管理对象的创建和依赖关系的框架或库

通过配置和约定来解析依赖关系，并自动将依赖对象注入到需要它们的地方

依赖注入有三种方式      

+ 构造函数注入
+ setter 方法注入
+ 方法注入
+ 接口注入

1. 构造函数注入

通过在构造函数中声明依赖对象的参数列表的方法来实现依赖注入

```
public class MyService
{
    private readonly IDependency _dependency;

    public MyService(IDependency dependency)
    {
        _dependency = dependency;

    }
}
```

在构造函数中声明依赖的接口或抽象类，通过构造函数参数将依赖对象传递进来。在使用 MyService 类时，依赖对象会被自动注入

2. setter 方法注入

```
public class MyService
{ 
    public IDependency Dependency { get; set; }
}
```

通过公共属性声明依赖的接口或抽象类，并提供 setter 方法。在使用 MyService 类时，依赖对象会被自动注入到属性中

3. 方法注入

```
 public class MyService
{
    public void SetDependency(IDependency dependency)
    {
        //...
    }
}
```

通过公共方法接收依赖的接口或抽象类作为参数，将依赖对象传递进来。在使用 MyService 类时，依赖对象可以通过调用方法进行注入

4.  接口注入
  ``` 
public class MyService
{
    private readonly IServiceLocator _serviceLocator;

    public MyService(IServiceLocator serviceLocator)
    {
        _serviceLocator = serviceLocator;
    }

    public void DoSomething()
    {
        IDependency dependency = _serviceLocator.Resolve<IDependency>();
        // 使用依赖对象进行操作
    }

    // ...
}
 ```

通过一个服务定位器接口或类，在需要使用依赖对象的地方通过解析器方法获取依赖对象

### 三. AutoFac

Autofac是一个开源的依赖注入（DI）和控制反转（IoC）容器，用于.NET应用程序的构建和管理对象的生命周期。
下面是 Autofac 支持的几种常见生命周期（以汽车和引擎为例）：

1. InstancePerDependency：每次依赖时创建一个新的实例。每次解析或注入时都会创建一个新的对象，对象的生命周期独立于其他对象。

       containerBuilder.RegisterType<Engine>().As<IEngine>().InstancePerDependency(); // 注册引擎类型

2. SingleInstance：单例模式，全局只创建一个实例。在整个应用程序生命周期内，只会创建一个对象实例，并在后续的解析或注入请求中重复使用该实例。

        containerBuilder.RegisterType<Engine>().As<IEngine>().SingleInstance(); // 注册引擎类型

3. InstancePerLifetimeScope：每个生命周期范围内创建一个实例。生命周期范围是一种逻辑上的容器，可以在其中创建和解析对象，通常与作用域（Scope）相关联。在同一个生命周期范围内，每次解析或注入都会得到相同的实例，但不同的生命周期范围会有不同的实例。

       containerBuilder.RegisterType<Engine>().As<IEngine>().InstancePerLifetimeScope(); // 注册引擎类型

4. InstancePerMatchingLifetimeScope：根据指定的标签（Tag）匹配生命周期范围。可以根据特定的标签创建和匹配生命周期范围，对象的生命周期将与匹配的生命周期范围相关联。

       containerBuilder.RegisterType<Engine>().As<IEngine>().InstancePerMatchingLifetimeScope(); // 注册引擎类型

5. InstancePerRequest：请求范围内创建一个实例。主要用于 Web 应用程序中，每个 HTTP 请求会创建一个对象实例，并在该请求的处理过程中重复使用该实例。

       containerBuilder.RegisterType<Engine>().As<IEngine>().InstancePerRequest(); // 注册引擎类型

6. InstancePerOwned：每个拥有对象（Owned）创建一个实例。Owned 实例是一种特殊的生命周期范围，用于管理对象的生命周期，并在对象不再需要时自动释放。

       containerBuilder.RegisterType<Engine>().As<IEngine>().InstancePerOwned(); // 注册引擎类型

### 四.ContainerBuilder

ContainerBuilder 是 AutoFac 中的一个类，它用于构建依赖注入容器（DI Container）的配置。

在 AutoFac 中，我们使用 ContainerBuilder 来注册和配置类型的依赖关系。它提供了一组方法，允许我们指定类型的实现以及它们的生命周期、参数传递方式等。

通过 ContainerBuilder，我们可以注册类型、指定类型之间的关系，例如接口和实现类的映射，还可以设置对象的生命周期等。

下面是一个简单的示例，展示如何使用 ContainerBuilder 创建一个简单的容器并注册依赖关系：

```
using Autofac;

class Program
{
    static void Main(string[] args)
    {
        // 创建 ContainerBuilder
        var builder = new ContainerBuilder();
    
        // 注册类型映射关系
        builder.RegisterType<MyDependency>().As<IDependency>();
    
        // 构建容器
        var container = builder.Build();
    
        // 从容器中解析所需的对象
        var dependency = container.Resolve<IDependency>();
    
        // 使用依赖对象 DoSomething() 是对应具体的的方法名
        dependency.DoSomething();
    
        Console.ReadLine();
    }
}
    
public interface IDependency
{
    void DoSomething();
}
    
public class MyDependency : IDependency
{
    public void DoSomething()
    {
        Console.WriteLine("Doing something.");
    }
} 
```

在上述示例中，我们创建了一个 ContainerBuilder 对象 builder，然后调用 builder.RegisterType<MyDependency>().As<IDependency>() 方法来注册 MyDependency 类作为 IDependency 接口的实现类。

最后，我们调用 builder.Build() 方法构建容器，然后通过容器的 Resolve<IDependency>() 方法从容器中解析出 IDependency 类型的对象。

使用 ContainerBuilder 可以方便地注册和配置依赖关系，它是 AutoFac 中的一个重要组件，用于构建和配置依赖注入容器。

### Example

一个完整的 “汽车（Car）和引擎（Engine）之间的关系，并使用 AutoFac 进行依赖注入，并指定了对象的生命周期”  的例子

1. 定义接口和类：

```
public interface IEngine
{
    void Start();
    void Stop();
}

public inter face ICar
{
    void Drive();
}

public class Engine : IEngine
{
    public void Start()
    {
        Console.WriteLine("Engine started.");
    }

    public void Stop()
    {
        Console.WriteLine("Engine stopped.");
    }
}

public class Car : ICar
{
    private readonly IEngine _engine;

    public Car(IEngine engine)
    {
        _engine = engine;
    }

    public void Drive()
    {
        _engine.Start();
        Console.WriteLine("Car is driving.");
        _engine.Stop();
    }
}
```

2. 配置 AutoFac 容器：

```
using Autofac;
  
class Program
{
    static void Main(string[] args)
    {
        // 创建 AutoFac 容器构建器
        var builder = new ContainerBuilder();
    
        // 注册接口和实现类
        builder.RegisterType<Engine>().As<IEngine>().SingleInstance();
        builder.RegisterType<Car>().As<ICar>();
    
        // 构建容器
        var container = builder.Build();
    
        // 从容器中解析所需的对象
        using (var scope = container.BeginLifetimeScope())
        {
            var car = scope.Resolve<ICar>();
    
            // 使用汽车对象
            car.Drive();
        }
    
        Console.ReadLine();
    }
}
```   

ps： 本文部分代码来源网络，知识点解读存在一定局限性