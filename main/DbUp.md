# DbUp

Dbup是一个开源的.NEßT库，用于数据库迁移和版本控制。可以用脚本的形式来进行管理数据库架构的变化，包括创建表、修改表结构、添加数据等操作

### DbUp的主要功能

脚本管理：您可以将数据库变更（如DDL语句、DML语句等）以脚本的形式编写，并将这些脚本存储在特定的目录中

自动化迁移：DbUp可以自动检测和应用尚未应用的脚本，以更新数据库的架构。它会按照脚本的版本号和执行顺序进行排序，并确保每个脚本只执行一次

事务支持：DbUp支持数据库事务，可以确保在执行脚本过程中的任何错误时，数据库会回滚到原始状态

多数据库支持：DbUp适用于多种主流的关系型数据库，包括SQL Server、MySQL、PostgreSQL等

### 配置和用例

#### 1. 配置

+ 下载 DbUp 的配置包

![image.png](https://upload-images.jianshu.io/upload_images/29476859-e760b8dc9de4594c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+  编写数据库脚本，以下采用的是 mysql 脚本

![image.png](https://upload-images.jianshu.io/upload_images/29476859-767a4fa74f93517b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ 并将脚本文件引入项目文件 .csproj

```
    <ItemGroup>
      <ProjectReference Include="..\PractiseForFreya.Message\PractiseForFreya.Message.csproj" />
    </ItemGroup>
```

+ 集成到应用程序中

```
using DbUp;

namespace PractiseForFreya.Core.DbUp;

public class DbUpRunner
{
    private readonly string _connectionString;

    public DbUpRunner(string connectionString)
    {
        _connectionString = connectionString;
    }

    public void Run()
    {
        // 创建或验证 MySQL 数据库是否存在
        EnsureDatabase.For.MySqlDatabase(_connectionString);

        var upgradeEngine = DeployChanges.To.MySqlDatabase(_connectionString)
            .WithScriptsEmbeddedInAssembly(typeof(DbUpRunner).Assembly).WithoutTransaction().LogToAutodetectedLog()
            .LogToConsole().Build();

        var result = upgradeEngine.PerformUpgrade();

        if (!result.Successful) throw result.Error;
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Success");
            Console.ResetColor();
        }
    }
}

```

   + 在 DbUp 中，`EnsureDatabase` 类提供了用于检查和创建数据库的方法
   + ` .For.MySqlDatabase() `方法指定了要执行操作的数据库类型
        + `EnsureDatabase.For.MySqlDatabase(connectionString).CreateDatabaseIfNotExists();` 创建或验证 MySQL 数据库是否存在,如果数据库不存在，则创建数据库
   + `DeployChanges` 是 DbUp 提供的一个静态类，用于创建数据库部署器，它允许指定要执行的数据库升级脚本，并执行数据库的版本控制和迁移
        + 使用 DeployChanges.To 链式调用创建数据库部署器
        + 通过链式调用其它方法来配置数据库升级的相关设置
        + 最后调用 Build() 方法构建部署器实例
    + `.WithScriptsEmbeddedInAssembly() `是 DbUp 中用于指定嵌入在程序集中的数据库升级脚本的方法
    + `.WithoutTransaction() `方法来告知 DbUp 在执行脚本时不使用事务
    + `.LogToAutodetectedLog()`方法自动检测日志记录器,跟踪日志
    + `.LogToConsole()`方法可以将升级过程中的日志信息输出到控制台
    + `.PerformUpgrade() 方法可以触发实际的数据库升级过程
+ 配置Program.cs 文件启动Dbup创建表结构

```
namespace PractiseForFreya.Api;

public class Program
{
    public static void Main(string[] args)
    {
        // 创建 ConfigurationBuilder 实例，加载配置文件
        var configuration = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddEnvironmentVariables()
            .Build();
        
       // 从配置文件中获取名为 "DbUp" 的连接字符串
       // GetConnectionString() 是 IConfiguration 接口中的一个方法，用于可以检索配置文件中 ConnectionStrings 节点下特定名称的连接字符串
        new DbUpRunner(configuration.GetConnectionString("DbUp")).Run();
    }
}
```

+ 配置文件appsettings.json 中要配置相关的链接字符串 “DbUp”

![image.png](https://upload-images.jianshu.io/upload_images/29476859-b4f39e292eb0d989.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以上配置完成后可以运行，运行结果如下

![image.png](https://upload-images.jianshu.io/upload_images/29476859-9ebdab5909bc3be6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

数据库会自动创建一个名为 schemaversions 的表格（可以根据需要指定其他表名），用于跟踪已经应用的数据库脚本版本，schemaversions 表会包含有关已应用脚本的信息，其中会记录每个已应用脚本的名称、应用时间以及其他相关信息，以便 DbUp 可以追踪哪些脚本已经执行过了，避免重复执行相同的脚本

同时 根据 mysql 脚本会生成表 user_question

