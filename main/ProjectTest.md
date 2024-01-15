# Project-Test

## 配置测试包

![image.png](https://upload-images.jianshu.io/upload_images/29476859-3bfa9fd78f93cd4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 配置测试的前提环境

### 1. 配置单元测试的基类

定义一个类，用于提供 一系列测试的方法，包括依赖注入、运行操作、以及带有工作单元的操作

#### 1. 存储依赖注入的声明周期

```
private ILifetimeScope _scope; // 存储依赖注入的生命周期范围

// 允许传入一个 ILifetimeScope 对象作为参数，以便在测试中使用依赖注入容器的生命周期范围。如果没有提供参数，_scope 将被设置为 null
protected TestUtilBase(ILifetimeScope scope = null)
{
    _scope = scope;
}

// 这个方法用于设置 _scope 字段，即设置依赖注入的生命周期范围。通过调用此方法，可以在类的实例中指定要使用的依赖注入容器的生命周期范围
protected void SetupScope(ILifetimeScope scope) => _scope = scope;
```

#### 2. 运行操作

针对不同数量的依赖项，创建一或多个运行操作，可以通过传递一个 Action<T> 委托来执行特定的操作

```
// Run<T> 接受一个 `T` 的依赖性 ,可以传递多个 Run<T,R,L> 
// Action<T> 传递一个 `Action<T>` 委托,可以传递多个 Action<T,R,L> 
protected void Run<T>(Action<T> action, Action<ContainerBuilder> extraRegistration = null)
{
    // 检查是否提供了 extraRegistration 参数，来确定是否需要使用额外的依赖项注册信息
    // 如果提供了，则使用这些额外的注册信息创建新的生命周期范围，并从中解析类型为 T 的依赖项
    var dependency = extraRegistration != null
        ? _scope.BeginLifetimeScope(extraRegistration).Resolve<T>()
        : _scope.BeginLifetimeScope().Resolve<T>();

    // 若传递多个的情况，可以如下
    /*
     var lifetime = extraRegistration != null
            ? _scope.BeginLifetimeScope(extraRegistration)
            : _scope.BeginLifetimeScope();

        var dependency = lifetime.Resolve<T>();
        var dependency2 = lifetime.Resolve<R>(); 
        var dependency3 = lifetime.Resolve<L>();
     */

    // 然后执行传入的操作
    action(dependency);

    // 若要执行多个参数传入操作
    // action(dependency, dependency2, dependency3);
}
```

#### 3. 异步运行操作

针对不同数量的依赖项，提供一或多个异步运行操作，可以通过传递一个Func<T> 委托来执行特定的操作，并提供对应的返回值

```
 protected async Task Run<T, R, L>(Func<T, R, L, Task> action, Action<ContainerBuilder> extraRegistration = null)
    {
        // 创建一个生命周期范围
        var lifetime = extraRegistration != null
            ? _scope.BeginLifetimeScope(extraRegistration)
            : _scope.BeginLifetimeScope();

        // 解析三个依赖项
        var dependency = lifetime.Resolve<T>();
        var dependency2 = lifetime.Resolve<R>();
        var dependency3 = lifetime.Resolve<L>();
        
        // 调用传递进来的异步函数，并等待其完成
        await action(dependency, dependency2, dependency3);
    }
```

注意⚠️： 没有 Async 的情况下，通常是需要返回值的，有Async 的情况下，看 Task 的情况决定返回值， 

#### 4. 异步运行带有工作单元的操作

```
  protected async Task RunWithUnitOfWork<T>(Func<T, Task> action, Action<ContainerBuilder> extraRegistration = null)
    {
        var scope = extraRegistration != null
            ? _scope.BeginLifetimeScope(extraRegistration)
            : _scope.BeginLifetimeScope();

        var dependency = scope.Resolve<T>();
        // 通过生命周期范围解析了类型为 T 的依赖项和类型为 IUnitOfWork 的工作单元
        var unitOfWork = scope.Resolve<IUnitOfWork>();
        
        await action(dependency);
        // 在异步操作完成后，调用工作单元的 SaveChangesAsync 方法，以确保对工作单元的任何更改都得以提交
        await unitOfWork.SaveChangesAsync();
    }
```

### 2. 配置单元测试类

通过 调用 单元测试基类的 SetupScope 函数，将传入的 scope 的生命周期赋值给基类，以供单元测试时使用统一的生命周期

```
public class TestUtil : TestUtilBase
{
    protected TestUtil(ILifetimeScope scope)
    {
        SetupScope(scope);
    }
}
```

### 3. 配置 测试类

在单元测试基类，单元测试类配置完成的基础上，配置 测试类

#### 1. 继承 基础 类

继承 单元测试基类，异步生命周期类，资源安全释放类

+ 单元测试基类 
    + 根据自身需求自定义 单元测试基类 
+ 异步生命周期类
    + Xunit 提供 异步生命周期接口 IAsyncLifetime
    + IAsyncLifetime 用于提供异步生命周期功能，目前支持以下情况：
        + 测试类（Test classes）
        + 在 IClassFixture{TFixture} 中使用的类
        + 在 ICollectionFixture{TFixture} 中使用的类
    + IAsyncLifetime接口定义了两个异步方法：
        + InitializeAsync: 在类被创建后立即调用，用于初始化操作
        + DisposeAsync: 在对象不再需要时调用，通常在 IDisposable.Dispose 之前调用，如果该类也实现了 IDisposable 接口
+ 资源安全释放类
    + IDisposable 接口描述了一个用于资源释放的模式，以解决确定性终结的问题
    + IDisposable 接口定义了一个方法 Dispose，该方法应满足以下条件：
        + 可以安全地多次调用
        + 释放与实例相关的任何资源
        + 调用基类的 Dispose 方法（如果有的话）
        + 抑制对此类的终结，以帮助 GC 通过减少终结队列上的对象数量
        + 通常不应引发异常，除非是非常严重且意外的错误（例如 OutOfMemoryException）。最好通过调用 Dispose 不应导致任何问题

#### 2. 配置数据库字符串、当前生命周期和容器

##### 1. 定义 测试 基础上 字段和属性

+ _testTopic：表示测试主题的字符串
+ _databaseName：表示数据库名称的字符串
+ ShouldRunDbUpDatabases：用于存储数据库是否应该运行 DbUp 数据库升级的标志
+ CurrentScope：表示当前生命周期范围的 ILifetimeScope 属性
+ Containers：表示一个特定的测试主题相关联的容器
+ CurrentConfiguration : 返回 CurrentScope 当前生命周期范围内解析的 IConfiguration 接口的实例

```
    private readonly string _testTopic;
    private readonly string _databaseName;
    
    private static readonly ConcurrentDictionary<string, IContainer> Containers = new();
    private static readonly ConcurrentDictionary<string,bool> ShouldRunDbUpDatabases = new();
    
    protected ILifetimeScope CurrentScope { get; }

    protected IConfiguration CurrentConfiguration => CurrentScope.Resolve<IConfiguration>();
```

##### 2. 测试类 配置 流程

+ 数据库链接
+ 注册模块和实例
+ 容器生成
+ Dispose（清除测试库数据）

1. 数据库链接

```
  private IConfiguration RegisterConfiguration(ContainerBuilder containerBuilder)
    {
        var targetJson = $"appsetting{_testTopic}.json";

        // 使用 File.Copy 方法从一个位置复制文件 appsettings.json 的路径到另一个位置 targetJson 的路径，若 targetJson 文件已存在，则允许覆盖
        File.Copy("appsettings.json", targetJson, true);
       
       // 读取指定路径 targetJson 的文件内容，并将读取到的 JSON 字符串反序列化为动态类型 (dynamic) 的对象 jsonObj
        dynamic jsonObj = JsonConvert.DeserializeObject(File.ReadAllText(targetJson));
     
         // 将 jsonObj 对象下的 ConnectionStrings 属性下的 Default 属性的值转换为字符串
        // 并使用 Replace 方法将字符串中的特定子串 "Database=smart_faq" 替换为 "Database={_databaseName}"
        jsonObj["ConnectionStrings"]["Default"] = jsonObj["ConnectionStrings"]["Default"].ToString()
            .Replace("Database=smart_faq", $"Database={_databaseName}");
       
        // 使用 JsonConvert.SerializeObject 方法将 jsonObj 对象序列化为 JSON 格式的字符串
        // 将序列化后的 JSON 字符串写入名为 targetJson 的文件中，覆盖原始文件内容
        File.WriteAllText(targetJson, JsonConvert.SerializeObject(jsonObj));
    
       // 构建一个 添加了targetJson 配置文件 配置对象 configuration
        var configuration = new ConfigurationBuilder()
            .AddJsonFile(targetJson)
            .Build();

        // 使用 Autofac 容器构建器 ContainerBuilder 的 RegisterInstance 方法
        // 将 configuration 对象注册为实现其接口的实例
        // AsImplementedInterfaces 方法指定注册的实例应该实现其所实现的所有接口
        containerBuilder.RegisterInstance(configuration).AsImplementedInterfaces();
        return configuration;
    }
```

2. 依赖和模块注入

```
private void RegisterBaseContainer(ContainerBuilder containerBuilder, IConfiguration configuration)
{
// 注册 与应用程序相关的依赖注入规则和配置 的模块
containerBuilder.RegisterModule(new PractiseForFreyaModule(configuration, typeof(PractiseForFreyaModule).Assembly));
      
// 注册 Substitute.For<IMemoryCache>() 对象实例
// 使用 NSubstitute 库创建一个模拟的 IMemoryCache 实例，以便在测试中隔离与内存缓存相关的逻辑  
containerBuilder.RegisterInstance(Substitute.For<IMemoryCache>()).AsImplementedInterfaces();
}
```

3. 容器生成

```
 protected TestBase(string testTopic, string databaseName)
    {
        _testTopic = testTopic;
        _databaseName = databaseName;

        var root = Containers.GetValueOrDefault(testTopic);

        if (root == null)
        {
            var containerBuilder = new ContainerBuilder();
            // 配置文件
            var configuration = RegisterConfiguration(containerBuilder);
            // 注册基础模块
            RegisterBaseContainer(containerBuilder,configuration);
            root = containerBuilder.Build();
            Containers[testTopic] = root;
        }

        CurrentScope = root.BeginLifetimeScope();
        // 是否使用DbUp 进行数据库迁移
        RunDbUpIfRequired();
        // 继承 测试单元基类 设置当前的生命周期范围
        SetupScope(CurrentScope);
    }

    private void RunDbUpIfRequired()
    {
        // 检查字典中是否包含与当前测试数据库名称 _databaseName 相关的条目
        // 该方法返回 false，并且不执行后续的数据库迁移代码
        if(!ShouldRunDbUpDatabases.GetValueOrDefault(_databaseName,true))
            return;
        // 如果数据库需要运行数据库迁移
        // 它创建一个 DbUpRunner 对象，并使用当前测试的配置 CurrentConfiguration 中的连接字符串进行初始化
        // 然后调用 Run() 方法执行数据库迁移
        new DbUpRunner(new ConnectionString(CurrentConfiguration).Value).Run();

        ShouldRunDbUpDatabases[_databaseName] = false;
    }
```

4. Dispose（清除测试库数据）

继承的 IAsyncLifetime异步生命周期类 会自动调用 方法 InitializeAsync 和  DisposeAsync
+ InitializeAsync: 在类被创建后立即调用，用于初始化操作
 + DisposeAsync: 在对象不再需要时调用，通常在 IDisposable.Dispose 之前调用，如果该类也实现了 IDisposable 接口

而 继承的 IDisposable资源释放类 会调用 Dispose 方法，我们可以自定义  Dispose 方法 ，在数据测试完毕时需要进行的一些关闭和清除操作可以在 Dispose 中 去完成

一般情况下，测试库的操作数据需要在测试完成之后，全部清除

```
  try
        {
            // 配置数据库链接
            var connection = new MySqlConnection(new ConnectionString(CurrentConfiguration).Value);

            var deleteStatements = new List<string>();
            // 数据库连接打开
            connection.Open();

           // 读取 当前测试环境 配置文件下，对应数据库下所有的数据表 表名
            using var reader =
                new MySqlCommand(
                        $"SELECT table_name FROM  INFORMATION_SCHEMA.tables WHERE table_schema = '{_databaseName}';",
                        connection)
                    .ExecuteReader();
            // 设置 MySQL 中的 SQL_SAFE_UPDATES 变量为 0
            // SQL_SAFE_UPDATES 控制 UPDATE 和 DELETE 语句的安全性
            // 当其为 1 时，UPDATE 和 DELETE 操作必须包含 WHERE 子句，以避免误操作
            // 将其设置为 0 允许在没有 WHERE 子句的情况下执行 UPDATE 和 DELETE
            deleteStatements.Add($"SET SQL_SAFE_UPDATES = 0");
            while (reader.Read())
            {
                var table = reader.GetString(0);

                if (!_tableRecordsDeletionExcludeList.Contains(table))
                {
                    deleteStatements.Add($"DELETE FROM `{table}`");
                }
            }

            deleteStatements.Add($"SET SQL_SAFE_UPDATES = 1");

            reader.Close();

            var strDeleteStatements = string.Join(";", deleteStatements) + ";";

            new MySqlCommand(strDeleteStatements, connection).ExecuteNonQuery();

            connection.Close();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error cleaning up data, {_testTopic},{ex}");
        }
```

注意⚠️： 测试库的配置文件 appsettings.json 主要是 建立一个与正式库区别开的测试库，由于正式库已存在 appsettings.json ，故要使 测试库的配置文件生效，需在项目的配置文件中更新配置的路径，如下：

```
   <ItemGroup>
        <None Update="appsettings.json">
            <CopyToOutputDirectory>Always</CopyToOutputDirectory>
        </None>
    </ItemGroup>
```

+ `<ItemGroup>`: 这是一个用于组织项目文件中项目元素的标记。在这里，用于组织文件处理的元素。
+ `<None Update="appsettings.json">`: 这是一个指定项目中无需编译的文件（例如配置文件）的元素。Update 属性用于指定文件的路径，这里是 appsettings.json
+ `<CopyToOutputDirectory>Always</CopyToOutputDirectory>`: 这个元素指定了文件在生成时要如何处理。CopyToOutputDirectory 元素设置文件的复制行为。Always 表示始终将文件复制到输出目录，无论是否发生更改

因此，这个 XML 片段的作用是告诉项目系统在构建项目时，将 appsettings.json 文件始终复制到输出目录（例如 bin\Debug 或 bin\Release）中。这通常用于确保配置文件在运行时能够在输出目录中找到

## 单元测试

### 单元测试操作

```
// 继承 单元测试类
public class SmartFaqUtil : TestUtil
{
    public SmartFaqUtil(ILifetimeScope scope) : base(scope)
    {
    }
    // 增删改查 请求， 例：
     public async Task Add(int id)
    {
        await RunWithUnitOfWork<IRepository>(async repository =>
        {
            await repository.InsertAsync(new Table { Id = id });
        });
    }
}
```

### 测试集合

可以使用 Collection 定义一个测试集合，并创建测试基类，该基类继承 最基础的测试基类，并在其构造函数中调用 最基础的测试基类的的构造函数，定义 测试主题_smartfaq_ 和 即将测试的数据库名称smart_faq

[Collection("SmartFaq Tests")] 是 xUnit 测试框架中的一个特性，它用于将测试类标记为属于指定的测试集合。测试集合用于对相关测试进行分组和协调执行

```
[Collection("SmartFaq Tests")]
public class SmartFaqFixtureBase : TestBase
{
    protected SmartFaqFixtureBase() : base("_smartfaq_", "smart_faq")
    {
    }
}
```

### 测试类

1.  继承已经 的数据库链接和各级别配置完成 的 测试基类
2. 在该测试类中的构造函数 创建单元测试实例，并将当前生命周期的 传递给 单元测试实例，以便后续 测试方法调用 单元测试实例中的 单元方法
3. 各 测试方法中 根据需要调用 单元测试类 中 的单元方法，并根据情况 进行具体断言 测试检查

```
// 继承 测试基类，测试时 根据 测试基类 找到对应的测试库和测试配置，独立测试主题
public partial class UserQuestionsFixture : SmartFaqFixtureBase
{
    private readonly SmartFaqUtil _smartFaqUtil;

    public UserQuestionsFixture()
    {
        // 创建单元测试实例，以便后续 的单元测试
        _smartFaqUtil = new SmartFaqUtil(CurrentScope);
    }

     [Fact]
     public async Task Test()
    {
        // 测试单元 中 添加 数据的 方法
        await _smartFaqUtil.Add(2, UserQuestionStatus.Pending, 1);
        // 获取 测试数据库 的数据
       var beforeUpdateQuestions = await Run<IRepository, List<UserQuestion>>(
            async repository => await repository.Query<UserQuestion>().Where(x => updateQuestionIds.Contains(x.Id))
         .ToListAsync(CancellationToken.None).ConfigureAwait(false));
         });

        foreach (var beforeUpdateQuestion in beforeUpdateQuestions)
        {
            var diffQuestion = beforeUpdateQuestions.Single(x => x.Id == beforeUpdateQuestion.Id);
            // 断言
            beforeUpdateQuestion.Status.ShouldBe(UserQuestionStatus.Annoying)(diffQuestion.Status);
            beforeUpdateQuestion.CorrectQid.ShouldBe(diffQuestion.CorrectQid);
        }
     }
}
```
