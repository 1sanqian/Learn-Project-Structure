# ORM 和 EF Core
## ORM

ORM（对象关系映射）是一种编程技术，用于在关系型数据库和面向对象编程语言之间建立映射关系。ORM 工具能够将数据库中的表、视图和存储过程等数据结构映射到编程语言中的对象模型，使得开发人员可以使用面向对象的方式来操作数据库，而不必直接编写 SQL 查询语句

ORM 的主要目标是简化数据库操作，它提供了一种更抽象的方式来处理数据库，隐藏了数据库底层细节

1. 面向对象编程： 将数据库表映射为编程语言中的对象，使得开发者可以使用面向对象的方式来处理数据，提高了代码的可读性和可维护性

2. 减少重复性工作： ORM 工具自动生成数据库操作的代码，减少了手动编写重复性 CRUD（创建、读取、更新、删除）操作的工作量

3. 数据库平台无关性： ORM 工具可以支持多种数据库类型，使得应用程序更易于迁移和适应不同的数据库平台

4. 提高生产力： ORM 工具提供了一些高级功能，如查询构建器、事务管理、缓存等，能够加快开发速度并提高效率

示例
+ 对象：c#中的对象
+ 关系：关系数据库
+ 映射：关系数据库和c#对象之间搭建的一座桥梁

如下通过创建c#对象的方式把数据插入数据库，省去编写Insert语句：

```
User user = new User（）{Name="li",Password="123456"};
orm.Save(user);
```

**注意：ORM只是对ADO.NET的封装，ORM底层任然是通过ADO.NET来访问数据库**

## EF Core 

Entity Framework (EF) Core 是流行的 Entity Framework 数据访问技术的轻量级、可扩展、开源和跨平台版本

EF Core用于将对象和数据库中的表进行映射，因此需要创建实体类和数据库表两项内容

使用 EF Core，可以使用 `模型` 执行数据访问，`模型` 由 实体类 和 表示与数据库的会话的上下文 对象组，上下文对象允许查询和保存数据

EF 支持以下模型开发方法：
+ 从现有数据库生成模型
+ 手动编写模型以匹配数据库
+ 创建模型后，使用 [EF 迁移](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/) 从模型 创建数据库
+ 迁移允许随着模型的变化而发展数据库

### EF Core 的基本工作流程（举例）
Entity Framework Core（EF Core）是一个对象关系映射（ORM）框架，用于在应用程序中处理数据库操作。它的基本工作流程通常包括以下几个步骤：

#### 举例
##### 1. 定义实体类

创建表示数据库表的实体类。每个实体类通常代表数据库中的一张表，类中的属性对应表中的列。

```
// 实体类：Blog
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; } // 表示博客包含多篇文章
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public int BlogId { get; set; } // 外键，表示文章所属博客

    public Blog Blog { get; set; } // 表示文章属于某个博客
}
```

##### 2. 创建数据库上下文类

派生自 DbContext 的类，它表示应用程序和数据库之间的会话。在这个类中，你可以定义实体集合（DbSet<>）以及配置数据库连接等信息。

```
// 数据库上下文类：BloggingContext
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        // 配置数据库连接
        optionsBuilder.UseSqlServer("YourConnectionString");
    }
}

```

##### 3. 配置实体之间的关系和映射

使用 Fluent API 或者数据注解（Data Annotations）来配置实体之间的关系，以及属性与数据库列之间的映射关系。这些配置用于定义数据库的结构。

```
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // 配置博客和文章之间的一对多关系
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)           // 一篇文章属于一个博客
            .WithMany(b => b.Posts)        // 一个博客拥有多篇文章
            .HasForeignKey(p => p.BlogId); // 使用 BlogId 作为外键

        // 其他配置，例如：索引、主键、唯一约束等
        // ...

        base.OnModelCreating(modelBuilder);
    }
}
```

##### 4. 进行数据库操作(执行 SQL 查询)

在应用程序中，通过数据库上下文类访问和操作实体集合。可以使用 LINQ 查询语言进行数据检索、插入、更新和删除操作等。

```
// 在应用程序中使用数据库上下文进行数据库操作
using (var context = new BloggingContext())
{
    // 添加新博客
    var newBlog = new Blog { Url = "https://example.com" };
    context.Blogs.Add(newBlog);
    context.SaveChanges();

    // 查询博客列表
    var blogs = context.Blogs.ToList();

    // 修改博客
    var blogToUpdate = context.Blogs.FirstOrDefault(b => b.Url == "https://example.com");
    if (blogToUpdate != null)
    {
        blogToUpdate.Url = "https://updated-url.com";
        context.SaveChanges();
    }

    // 删除博客
    var blogToDelete = context.Blogs.FirstOrDefault(b => b.Url == "https://updated-url.com");
    if (blogToDelete != null)
    {
        context.Blogs.Remove(blogToDelete);
        context.SaveChanges();
    }
}
```

#### 执行数据库迁移

EF Core 的迁移系统允许你基于模型更改来更新数据库架构。通过执行 add-migration 命令来创建迁移，然后使用 update-database 命令来应用迁移，将模型更改同步到数据库

```
dotnet ef migrations add InitialCreate
dotnet ef database update

```

## EF Core 的相关知识点

### 实体

#### 实体类型

##### 在模型中包含类型

按照约定，上下文中的 DbSet 属性中公开的类型将作为实体包含在模型中。还包括方法中指定的实体类型OnModelCreating，以及通过递归探索其他已发现实体类型的导航属性找到的任何类型

```
/*
在下面的代码示例中，包含所有类型：
Blog包含在内是因为它在上下文的 DbSet 属性中公开。
Post包含在内是因为它是通过Blog.Posts导航属性发现的。
AuditEntry因为它是在 中指定的OnModelCreating
*/
internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

##### 从模型中排除类型

```
// 排除某种类型 ：Entity Framework Core 不会将这个类映射到数据库中的任何表
// 应用在特定属性上，则只有该属性不会被映射到数据库表中的字段
[NotMapped]
public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```

**从迁移中排除**

有时将相同的实体类型映射到多个类型中很有用`DbContext`。在使用[有界上下文](https://www.martinfowler.com/bliki/BoundedContext.html)时尤其如此，因为`DbContext`每个有界上下文通常都有不同的类型

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<IdentityUser>()
        .ToTable("AspNetUsers", t => t.ExcludeFromMigrations());
}
// 使用此配置，迁移将不会创建AspNetUsers表，但IdentityUser仍包含在模型中并且可以正常使用
// 如果需要再次开始使用迁移来管理表，则应在不排除的情况下创建新的迁移AspNetUsers。下一次迁移现在将包含对表所做的任何更改
```

##### 表名

按照约定，每个实体类型都将设置为映射到与公开该实体的 DbSet 属性同名的数据库表。如果给定实体不存在 DbSet，则使用类名

```
[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

##### 表架构

使用关系数据库时，按照惯例，表是在数据库的默认架构中创建的

```
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

// 或 使用 Fluent API 在模型级别定义默认架构，而不是为每个表指定架构
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    // 用于配置数据库模式的默认架构 为 blogging
    modelBuilder.HasDefaultSchema("blogging");
}
```

##### 查看映射

可以使用 Fluent API 将实体类型映射到数据库视图

```
// EF 将假定引用的视图已存在于数据库中，它不会在迁移中自动创建它
modelBuilder.Entity<Blog>()
    .ToView("blogsView", schema: "blogging");
// 映射到视图将删除默认的表映射，但实体类型也可以显式映射到表。在这种情况下，查询映射将用于查询，表映射将用于更新
```

##### 表值函数映射

可以将实体类型映射到表值函数 (TVF)，而不是数据库中的表

```
public class BlogWithMultiplePosts
{
    public string Url { get; set; }
    public int PostCount { get; set; }
}

// 在数据库中创建以下表值函数
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)

// 实体BlogWithMultiplePosts可以通过以下方式映射到该函数
// 为了将实体映射到表值函数，该函数必须是无参数的
modelBuilder.Entity<BlogWithMultiplePosts>().HasNoKey().ToFunction("BlogsWithMultiplePosts");

// 按照惯例，实体属性将映射到 TVF 返回的匹配列。如果 TVF 返回的列的名称与实体属性的名称不同，则可以使用HasColumnName方法配置实体的列，就像映射到常规表时一样

var query = from b in context.Set<BlogWithMultiplePosts>()
            where b.PostCount > 3
            select new { b.Url, b.PostCount };
```

##### 表评论

可以设置在数据库表上设置的任意文本注释，从而在据库中记录模式

```
[Comment("Blogs managed on the website")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

##### 共享型实体类型

使用相同 CLR 类型的实体类型称为共享类型实体类型。这些实体类型需要配置一个唯一的名称，除了 CLR 类型之外，每当使用共享类型实体类型时都必须提供该名称。这意味着DbSet必须使用Set调用来实现相应的属性

```
internal class MyContext : DbContext
{
    public DbSet<Dictionary<string, object>> Blogs => Set<Dictionary<string, object>>("Blog");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>(
            "Blog", bb =>
            {
                bb.Property<int>("BlogId");
                bb.Property<string>("Url");
                bb.Property<DateTime>("LastUpdated");
            });
    }
}
```
#### 实体属性

模型中的每个实体类型都有一组属性，EF Core 将从数据库中读取和写入这些属性。如果您使用的是关系数据库，实体属性将映射到表列

##### 包含和排除的属性

public class Blog
{
  // 正常包含
    public int BlogId { get; set; }
    public string Url { get; set; }

    // 注解 NotMapped 表不排除
    [NotMapped]
    public DateTime LoadedFromDatabase { get; set; }
}

#####  常见属性

```
public class Blog
{
    // 列名称
    // 使用关系数据库时，实体属性将映射到与属性同名的表列
    [Column("blog_id")]
    public int BlogId { get; set; }

    // 列数据类型
    // 使用关系数据库时，数据库提供程序根据属性的 .NET 类型选择数据类型。它还考虑其他元数据，例如配置的最大长度、属性是否是主键的一部分等
    [Column(TypeName = "varchar(200)")]
    public string Url { get; set; }

    [Column(TypeName = "decimal(5, 2)")]
    public decimal Rating { get; set; }

    // 最大长度
    [MaxLength(500)]
    public string UserName { get; set; }

    // 精度和规模
    Precision(14, 2)]
    public decimal Score { get; set; }
    [Precision(3)]
    public DateTime LastUpdated { get; set; }

    // 统一码
    [Unicode(false)]
    [MaxLength(22)]
    public string Isbn { get; set; }

    // 必需和可选属性
    [Required] // Data annotations needed to configure as required
    public string FirstName { get; set; }

    [Required] // Data annotations needed to configure as required
    public string LastName { get; set; }

    // 专栏评论
    // [Comment("The MiddleName of the blog")]
    public string MiddleName { get; set; } // Optional by convention

    // 列顺序
    [Column(Order = 0)]
    public string age { get; set; }
}
```

##### 列排序规则

可以在文本列上定义排序规则，确定它们的比较和排序方式

```
modelBuilder.Entity<Customer>().Property(c => c.Name)
    .UseCollation("SQL_Latin1_General_CP1_CI_AS");
```

#### 实体按键

键充当每个实体实例的唯一标识符。EF 中的大多数实体都有一个键，这映射到关系数据库中主键的概念

##### 配置主键

Id按照约定，名为或 的属性<type name>Id将被配置为实体的主键

```
internal class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}

// 将单个属性配置为实体的主键

internal class Car
{
    [Key]
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}

// 可以将多个属性配置为实体的键 - 这称为复合键。约定只会在特定情况下设置复合键 - 例如对于拥有的类型集合
[PrimaryKey(nameof(State), nameof(LicensePlate))]
internal class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

##### 主键名称

按照惯例，在关系数据库上，主键是使用名称创建的PK_<type name>。您可以配置主键约束的名称，如下所示：

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .HasKey(b => b.BlogId)
        .HasName("PrimaryKey_BlogId");
}
```

##### 备用键

除了主键之外，备用键还充当每个实体实例的备用唯一标识符；它可以用作关系的目标。当使用关系数据库时，这映射到备用键列上的唯一索引/约束以及引用该列的一个或多个外键约束的概念

```
internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .HasForeignKey(p => p.BlogUrl)
            .HasPrincipalKey(b => b.Url);  // 配置 url 为备用键
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public string BlogUrl { get; set; }
    public Blog Blog { get; set; }
}
```

##### 配置单个属性为备用键

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasAlternateKey(c => c.LicensePlate);
}
```

##### 配置多个属性为备用键（复合备用键）

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasAlternateKey(c => new { c.State, c.LicensePlate });
}
```

按照惯例，将为备用键引入的索引和约束将被命名AK_<type name>_<property name>（对于复合备用键，<property name>将成为下划线分隔的属性名称列表）

可以配置备用键的索引名称和唯一约束

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasAlternateKey(c => c.LicensePlate)
        .HasName("AlternateKey_LicensePlate");
}
```

### 模型

使用 EF Core，可以使用 `模型` 执行数据访问，`模型` 由 实体类 和 表示与数据库的会话的上下文 对象组，上下文对象允许查询和保存数据

EF Core用于将对象和数据库中的表进行映射，因此需要创建实体类和数据库表两项内容

EF 支持以下模型开发方法：

+ 从现有数据库生成模型
+ 手动编写模型以匹配数据库
+ 创建模型后，使用 [EF 迁移](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/) 从模型 创建数据库
+ 迁移允许随着模型的变化而发展数据库

#### DbContext 生命周期、配置和初始化

##### DbContext 生命周期

使用 Entity Framework Core (EF Core) 时的典型工作单元涉及：

*   创建`DbContext`实例
*   通过上下文跟踪实体实例。实体被跟踪
    *   [从查询](https://learn.microsoft.com/en-us/ef/core/querying/tracking)返回[](https://learn.microsoft.com/en-us/ef/core/querying/tracking)
    *   添加[或附加到上下文](https://learn.microsoft.com/en-us/ef/core/saving/disconnected-entities)
*   根据需要对跟踪实体进行更改以实施业务规则
*   [](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges)调用[SaveChanges](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges)或[SaveChangesAsync ](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync)，EF Core 检测所做的更改并将其写入数据库
*   实例`DbContext`已处置

***注意：***
*   使用后处理[DbContext](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext)非常重要。这确保了释放任何非托管资源，并且取消注册任何事件或其他挂钩，以防止在实例仍然被引用的情况下发生内存泄漏
*   DbContext**不是线程安全的**。不要在线程之间共享上下文。确保在继续使用上下文实例之前等待所有[异步调用](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/await)
*   EF Core 代码引发的InvalidOperationException可能会将上下文置于不可恢复的状态。此类[异常](https://learn.microsoft.com/en-us/dotnet/api/system.invalidoperationexception)表示程序错误，并且不能从中恢复

##### 依赖注入中的DbContext

在许多 Web 应用程序中，每个 HTTP 请求对应一个工作单元。这使得将上下文生命周期与请求生命周期联系起来成为 Web 应用程序的良好默认设置

ASP.NET Core 应用程序是使用依赖注入进行配置的。可以使用的方法中的[AddDbContext](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)将 EF Core 添加到此配置

```
// 依赖注入容器
// ASP.NET Core 应用程序服务提供程序中注册一个DbContext称为作用域服务的子类
// ApplicationDbContext上下文配置为使用 SQL Server 数据库提供程序，并将从 ASP.NET Core 配置中读取连接字符串
// 通常，在何处ConfigureServices进行调用并不重要AddDbContext
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    services.AddDbContext<ApplicationDbContext>(
        options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
}

// 类ApplicationDbContext必须公开带有参数的公共构造函数DbContextOptions<ApplicationDbContext>
// 上下文配置AddDbContext传递到DbContext
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}

// ApplicationDbContext然后可以通过构造函数注入在 ASP.NET Core 控制器或其他服务中使用
public class MyController
{
    private readonly ApplicationDbContext _context;

    public MyController(ApplicationDbContext context)
    {
        _context = context;
    }
}

//最终结果是ApplicationDbContext为每个请求创建一个实例，并传递给控制器以执行工作单元，然后在请求结束时进行处理
```

##### “new” DbContext 初始化

DbContext实例可以用普通的 .NET 方式构造，可以通过重写OnConfiguring方法或将选项传递给构造函数来执行配置

```
public class ApplicationDbContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
    }
}

// 或 通过DbContext构造函数传递连接字符串等配置
public class ApplicationDbContext : DbContext
{
    private readonly string _connectionString;

    public ApplicationDbContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connectionString);
    }
}

// 或 DbContextOptionsBuilder可用于创建一个DbContextOptions对象，然后将其传递给DbContext构造函数。这允许DbContext显式构建配置为依赖注入的配置
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) ：base(options)
    {
    }
}

// 可以DbContextOptions创建并显式调用构造函数
// 可以在 MyController 中调用
var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                                             .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                                             .Options;
using var context = new ApplicationDbContext(contextOptions);
```

##### 使用 DbContext 工厂
在这些情况下，AddDbContextFactory可用于注册用于创建实例的工厂DbContext： 
+ 1. 某些应用程序类型（例如ASP.NET Core Blazor）使用依赖项注入，但不会创建与所需DbContext生命周期一致的服务范围
+ 2. 应用程序也可能需要在此范围内执行多个工作单元，例如单个Http请求中的多个工作单元

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContextFactory<ApplicationDbContext>(options => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
}
```

该类ApplicationDbContext必须公开带有参数的公共构造函数DbContextOptions<ApplicationDbContext>。这与上面传统 ASP.NET Core 部分中使用的模式相同

```
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}

// DbContextFactory然后可以通过构造函数注入在其他服务中使用该工厂
private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
{
    _contextFactory = contextFactory;
}

// 然后，注入的工厂可用于在服务代码中构造 DbContext 实例
public void DoSomething()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}

//DbContext以这种方式创建的实例不由应用程序的服务提供商管理，因此必须由应用程序处置
```

##### 数据库上下文选项

所有配置的起点`DbContext`是[DbContextOptionsBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder)。可以通过三种方式获取此构建器：

*   `AddDbContext`相关方法
*   `OnConfiguring`
*   显式构造为`new`

##### 配置数据库提供者

每个DbContext实例必须配置为使用一个且仅一个数据库提供程序。（子DbContext类型的不同实例可以与不同的数据库提供程序一起使用，但单个实例只能使用一个。）数据库提供程序是使用特定Use*调用来配置的。例如，要使用 SQL Server 数据库提供程序：

```
public class ApplicationDbContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        // 这些Use*方法是数据库提供者实现的扩展方法。这意味着必须先安装数据库提供程序 NuGet 包，然后才能使用扩展方法
        optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
    }
}
```

ps： DbContext 配置和初始化： https://learn.microsoft.com/en-us/ef/core/dbcontext-configuration/


#### 创建模型

EF Core 使用元数据*模型*来描述应用程序的实体类型如何映射到底层数据库。该模型是使用一组[*约定构建*](https://learn.microsoft.com/en-us/ef/core/modeling/#built-in-conventions)的 - 寻找常见模式的启发式方法。然后可以使用[映射属性（也称为*数据注释*）](https://learn.microsoft.com/en-us/ef/core/modeling/#use-data-annotations-to-configure-a-model)和/或调用[OnModelCreating](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)中的[ModelBuilder](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.modelbuilder)方法[（也称为*Fluent API*）](https://learn.microsoft.com/en-us/ef/core/modeling/#use-fluent-api-to-configure-a-model)来自定义模型，这两者都将覆盖按约定执行的配置

##### 使用 Fluent API 配置模型

Fluent API 配置具有最高优先级，并将覆盖约定和数据注释。可以在派生上下文中重写 OnModelCreating 方法并使用 Fluent API 配置模型，这种方法允许在不修改实体类的情况下指定配置

```
using Microsoft.EntityFrameworkCore;

namespace EFModeling.EntityProperties.FluentAPI.Required;

internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    #region Required
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // ModelBuilder 来配置实体类与数据库之间的映射关系和数据库架构
        // 指定 实体类 Blog 的字段属性 Url 为必需字段
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .IsRequired();
    }
    #endregion
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

##### 分组配置

为了减少方法的大小，`OnModelCreating`实体类型的所有配置都可以提取到实现[IEntityTypeConfiguration<TEntity>](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.ientitytypeconfiguration-1)的单独类中

```
public class BlogEntityTypeConfiguration : IEntityTypeConfiguration<Blog>
{
    public void Configure(EntityTypeBuilder<Blog> builder)
    {
        builder
            .Property(b => b.Url)
            .IsRequired();
    }
}
// 只需调用Configure中的方法即可OnModelCreating
new BlogEntityTypeConfiguration().Configure(modelBuilder.Entity<Blog>());
```

##### 应用装配体中的所有配置

```
// 应用配置的顺序是未定义的，因此仅当顺序无关紧要时才应使用此方法
modelBuilder.ApplyConfigurationsFromAssembly(typeof(BlogEntityTypeConfiguration).Assembly);
```

##### EntityTypeConfigurationAttribute在实体类型上使用

可以将[EntityTypeConfigurationAttribute](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.entitytypeconfigurationattribute)放置在实体类型上`Configure`，而不是显式调用，以便 EF Core 可以找到并使用适当的配置

```
// EntityTypeConfigurationAttribute类型不会在程序集中自动发现。必须先将实体类型添加到模型中，然后才能在该实体类型上发现属性
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
} 

// 此属性意味着每当模型中包含实体类型IEntityTypeConfiguration时，EF Core 将使用指定的实现
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
}

//  或者通过在OnModelCreating中注册它
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

#### 使用数据注释来配置模型

将某些属性（称为数据注释）应用于类和属性。数据注释将覆盖约定，但会被 Fluent API 配置覆盖

```
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using Microsoft.EntityFrameworkCore;

namespace EFModeling.EntityProperties.DataAnnotations.Annotations;

internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
}

[Table("Blogs")]
public class Blog
{
    public int BlogId { get; set; }

    [Required]
    public string Url { get; set; }
}
```

### 查询数据
[使用语言集成查询 (LINQ)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/) 从数据库中检索实体类的示例

```
using(var db = new BloggingContext()){
var blogs = db.Blogs.Where(b=>b.Rating >3)
                  .OriderBy(b=>b.Url)
                  .ToList()
}

// 加载所有数据
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.ToList();
}

// 加载单个实体
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Single(b => b.BlogId == 1);
}

// 过滤
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Where(b => b.Url.Contains("dotnet"))
        .ToList();
}
```

##### 追踪查询

默认情况下，返回实体类型的查询正在跟踪。跟踪查询意味着对实体实例的任何更改都会由SaveChanges保存到

```
var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
blog.Rating = 5;
context.SaveChanges();
```
当跟踪查询返回结果时，EF Core 会检查实体是否已在上下文中。如果 EF Core 找到现有实体，则会返回相同的实例，这可能会使用更少的内存，并且比无跟踪查询更快。EF Core 不会使用数据库值覆盖条目中实体属性的当前值和原始值。如果在上下文中找不到该实体，EF Core 将创建一个新的实体实例并将其附加到上下文。查询结果不包含任何已添加到上下文但尚未保存到数据库的实体

##### 不追踪查询

当结果用于只读场景时，无跟踪查询非常有用。它们通常执行速度更快，因为无需设置更改跟踪信息。如果不需要更新从数据库检索的实体，则应使用无跟踪查询。单个查询可以设置为不跟踪。无跟踪查询还根据数据库中的内容给出结果，而不考虑任何本地更改或添加的实体

```
var blogs = context.Blogs
    .AsNoTracking()
    .ToList();

// 在上下文实例级别中更改默认追踪行为

context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
var blogs = context.Blogs.ToList();
```

##### 预加载

使用Include方法指定要包含在查询结果中的相关数据

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .ToList();
}
```

可以在单个查询中包含多个关系中的相关数据

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .ToList();
}
```

##### 包括多个级别

包括多个级别

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .ThenInclude(post => post.Author)
        .ToList();
}
```

链接多个调用以ThenInclude继续包含更多级别的相关数据

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .ThenInclude(post => post.Author)
        .ThenInclude(author => author.Photo)
        .ToList();
}
```

可以组合所有调用，以在同一查询中包含来自多个级别和多个根的相关数据

```
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Include(blog => blog.Posts)
        .ThenInclude(post => post.Author)
        .ThenInclude(author => author.Photo)
        .Include(blog => blog.Owner)
        .ThenInclude(owner => owner.Photo)
        .ToList();
}
```

ps: 查询数据的追踪与不追踪：https://learn.microsoft.com/en-us/ef/core/querying/tracking

### 保存数据 


使用实体类的实例在数据库中创建、删除和修改数据

```
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}

// 更改跟踪和SaveChanges
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Single(b => b.Url == "http://example.com");
    blog.Url = "http://example.com/blog";
    context.SaveChanges();
}

// 执行更新和执行删除（批量更新）

// 1. 使用ExecuteDelete
context.Blogs.Where(b => b.Rating < 3).ExecuteDelete();
// 2. 通过常规 LINQ 运算符表达 SQLDELETE语句 - 类似于常规 LINQ 查询 - 导致对数据库执行以下 SQL
DELETE FROM [b]
FROM [Blogs] AS [b]
WHERE [b].[Rating] < 3

//更新
// 1.
context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdate(setters => setters.SetProperty(b => b.IsVisible, false));
// 2.
UPDATE [b]
SET [b].[IsVisible] = CAST(0 AS bit)
FROM [Blogs] AS [b]
WHERE [b].[Rating] < 3
```

#### 添加新实体的图表

创建多个新的相关实体，将其中一个添加到上下文中将导致其他实体也被添加

```
using (var context = new BloggingContext())
{
    var blog = new Blog
    {
        Url = "http://blogs.msdn.com/dotnet",
        Posts = new List<Post>
        {
            new Post { Title = "Intro to C#" },
            new Post { Title = "Intro to VB.NET" },
            new Post { Title = "Intro to F#" }
        }
    };

    context.Blogs.Add(blog);
    context.SaveChanges();
}
//使用 EntityEntry.State 属性仅设置单个实体的状态。例如，context.Entry(blog).State = EntityState.Modified
```

#### 添加相关实体

从上下文已跟踪的实体的导航属性引用新实体，则该实体将被发现并插入到数据库中

```
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Include(b => b.Posts).First();
    var post = new Post { Title = "Intro to EF Core" };

    blog.Posts.Add(post);
    context.SaveChanges();
}
```

#### 改变关系

更改实体的导航属性，数据库中的外键列也会发生相应的更改

```
using (var context = new BloggingContext())
{
    var blog = new Blog { Url = "http://blogs.msdn.com/visualstudio" };
    var post = context.Posts.First();

    post.Blog = blog;
    context.SaveChanges();
}
```

#### 删除关系

`null` :可以通过将引用导航设置为或从集合导航中删除相关实体来删除关系

根据关系中配置的级联删除行为，删除关系可能会对依赖实体产生副作用

默认情况下，对于所需的关系，会配置级联删除行为，并且将从数据库中删除子/依赖实体。对于可选关系，默认不配置级联删除，但外键属性将设置为 null

请参阅[必需和可选关系](https://learn.microsoft.com/en-us/ef/core/modeling/relationships#optional-and-required-relationships)以了解如何配置关系的必需性

有关级联删除行为如何工作、如何显式配置以及如何按约定选择它们的更多详细信息，请参阅[级联删除](https://learn.microsoft.com/en-us/ef/core/saving/cascade-delete)

```
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Include(b => b.Posts).First();
    var post = blog.Posts.First();

    blog.Posts.Remove(post);
    context.SaveChanges();
}
```
#### 级联删除

Entity Framework Core (EF Core) 使用外键表示关系。具有外键的实体是关系中的子实体或依赖实体。该实体的外键值必须与相关主体/父实体的主键值（或备用键值）匹配

如果删除主体/父实体，则从属/子实体的外键值将不再与任何主体/父实体的主键或备用键匹配。这是无效状态，并且会在大多数数据库中导致引用约束违规

有两个选项可以避免违反引用约束：
+ 将 FK 值设置为 null
+ 同时删除依赖/子实体

第一个选项仅对外键属性（及其映射到的数据库列）必须可为空的可选关系有效

第二个选项对任何类型的关系都有效，称为“级联删除”

##### 当级联行为发生时

依赖/子实体不再与其当前主体/父实体关联时，需要级联删除。发生这种情况的原因可能是主体/父项被删除，或者当主体/父项仍然存在但依赖项/子项不再与其关联时，可能会发生这种情况

##### 删除级联
```
// 考虑以上模型，其中Blog是 的关系中的主体/父项Post， 是从属/子项。Post.BlogId是一个外键属性，其值必须Blog.Id与帖子所属博客的主键匹配
// 按照约定，Post.BlogId 是Bolg外键关系被配置为必需的，因为Post.BlogId外键属性不可为空。默认情况下，所需关系配置为使用级联删除

using var context = new BlogsContext();
var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();
context.Remove(blog);
context.SaveChanges();
```

##### 断绝关系

可以切断表之间的关系，通过将表的参考导航设置为 null 来完成

```
using var context = new BlogsContext();
var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();
foreach (var post in blog.Posts)
{
    post.Blog = null;
}
context.SaveChanges();
```

或者刪除表之间联系的一个表外键来切断关系

```
using var context = new BlogsContext();
var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();
blog.Posts.Clear();
context.SaveChanges();
```

ps： 级联删除： https://learn.microsoft.com/en-us/ef/core/saving/cascade-delete
