# Hangfire

Hangfire 提供一种简单而可靠的方法来处理异步任务，定时任务和长时间运行的任务，且允许在后台执行任务，不会阻塞应用程序的主线程(主要用来处理耗时的操作、异步和队列)

## 主要功能

+ 后台任务调度
    + 允许安排执行后台任务，任务可以是定期运行的、延迟运行的、或在特点添加下触发的
+ 分布式任务处理
    + hangfire 支持分布式环境下的任务处理，可以多个服务器上运行后台任务，不需要复杂的设置
+ 持久性存储
    + 任务消息和状态会被持久性地存储，通常是在数据库中，以确保任务在应用程序重写启动后继续执行，支持多种存储后端，如 SQL Server、MySQL、Redis 等
+ 监控和管理
    + 提供一个直观的仪表盘，用于监控和管理后台任务的执行情况，包括任务的历史记录、执行时间等

## Hangfire 配置 和 使用

### 配置包

Hangfire 主要包文件如下：
+ Hangfire.AspNetCore： 主要用于实现后台任务调度和处理的功能
+ Hangfire.Core：包含 Hangfire 的基本功能和通用的后台任务调度和处理机制，并提供了任务队列、调度器、任务执行器等基础组件
+ Hangfire.MySqlStorage(根据情况选择对应的数据库)：Hangfire 提供的 MySQL 存储支持，用于将 Hangfire 的任务信息和状态持久性地存储在 MySQL 数据库中

![image.png](https://upload-images.jianshu.io/upload_images/29476859-6c6aef8472cd83cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 配置Hangfire

在项目的 startup.cs 文件中，在配置应用程序的服务容器 ConfigureServices方法中，添加 hangfire     的配置，提供一个后台存储用的数据库，以便存储任务消息

```
public void ConfigureServices(IServiceCollection services)
{
     // 用于配置和注册 Hangfire 相关的服务
     services.AddHangfire(config => config.UseMemoryStorage());

     // 也可以进行自定义配置 Hangfire 相关的服务
     services.AddHangFireService(Configuration); 

    // 若要使用 mysql 存储，可以配置以下
    // services.AddHangfire(config => config.UseStorage(new MySqlStorage("your_mysql_connection_string")));
}
```

关于 Hangfire 自定义的服务可以参考以下

```
public static class HangFireExtension
{
    public static void AddHangFireService(this IServiceCollection services, IConfiguration configuration)
    {
       services.AddHangfire(config => config
            .SetDataCompatibilityLevel(CompatibilityLevel.Version_170)
            .UseSimpleAssemblyNameTypeSerializer()
            .UseRecommendedSerializerSettings()
            .UseStorage(
                new MySqlStorage(
                    configuration.GetConnectionString("Default"),
                    new MySqlStorageOptions
                    {
                        QueuePollInterval = TimeSpan.FromSeconds(10),
                        JobExpirationCheckInterval = TimeSpan.FromHours(1),
                        CountersAggregateInterval = TimeSpan.FromMinutes(5),
                        PrepareSchemaIfNecessary = true,
                        DashboardJobListLimit = 25000,
                        TransactionTimeout = TimeSpan.FromMinutes(1),
                        TablesPrefix = "Hangfire"
                    }
                )
            ));

        // 将 Hangfire 作为 IHostedService 添加到服务集合中。这将在应用程序启动时启动 Hangfire 任务处理服务器，并设置工作线程数为 1
        services.AddHangfireServer(options => options.WorkerCount = 1);
    }
}
```

+ services.AddHangfire() :  使用 AddHangfire 方法来配置 Hangfire 的服务
+ config.SetDataCompatibilityLevel() ：设置Hangfire 存储的数据兼容性级别
+ .UseSimpleAssemblyNameTypeSerializer()：使用简单的程序集名称类型序列化器。这是一种用于序列化和反序列化 Hangfire 任务数据的方法
+ .UseRecommendedSerializerSettings()： 使用推荐的序列化器设置。这将使用推荐的序列化器选项进行配置
+ .UseStorage(): 使用 MySQL 存储
+  new MySqlStorage()：使用 MySqlStorage 作为 存储方式
+ configuration.GetConnectionString()  ：使用配置文件中 规范的数据库连接字符串用于连接到 MySQL 数据库        
+ new MySqlStorageOptions(): 用于配置或设置与 MySql 存储相关的选项
+ QueuePollInterval: 队列轮询间隔
+ JobExpirationCheckInterval：作业过期检查间隔
+ CountersAggregateInterval：计数器聚合间隔
+ PrepareSchemaIfNecessary：需要时自动准备 Hangfire 数据库模式，为 true 时，如果数据库模式不存在，Hangfire 将自动创建所需的表和架构 
+ DashboardJobListLimit：指定在 Hangfire 仪表板上显示的作业列表的最大数量
+ TransactionTimeout：事务超时的设置，用于指定 Hangfire 在执行作业时的事务超时时间
+ TablesPrefix： 为 数据库表添加一个前缀，以避免与其他表名冲突

### 配置 Hangfire Dashboard 和 Hangfire Server

Startup.cs 文件的配置应用程序的 HTTP 请求处理管道 Configure 方法中，配置Hangfire Dashboard 和 Hangfire Server，以便查看任务执行的情况

```
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
     app.UseHangfireServer();   // 用于将 Hangfire 任务处理服务器添加到请求处理管道中
     app.UseHangfireDashboard();    // 将 Hangfire 仪表板添加到应用程序的请求处理管道中
}
```

###  Hangfire数据库表

1. 项目配置以上操作后，运行项目，可以看见数据库自动生成的许多 hangfire 相关的表，这些表都是hangfire用来执行持久化任务

![image.png](https://upload-images.jianshu.io/upload_images/29476859-7f0ef50ee43a1ea0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 打开项目路由到 hangfire 路径，可以看见hangfire 的仪表盘

![image.png](https://upload-images.jianshu.io/upload_images/29476859-9cedd83ac428f731.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

尚未开始使用hangfire时，仪表盘时空的

## Hangfrie 的多种类型的后台作业

### Fire-and-forget

Fire-and-forget（一次性任务）： 仅执行一次，不需要等待结果
+ 无需等待：调用方不需要等待任务的完成，可以继续执行后续的代码
+ 执行一次："Fire-and-forget jobs" 是一次性任务，执行完成后不会再次触发
+ 适用场景：适用于那些不需要立即获得结果的任务，例如发送电子邮件、生成报告等
+ BackgroundJob.Enqueue ：用于创建一个 "Fire-and-forget job"
    +  接受一个委托，表示要执行的后台任务

```
var jobId = BackgroundJob.Enqueue(() => Console .WriteLine( "Fire-and-forget job executed! ));
```

### Delayed

Delayed jobs（延迟任务）：在指定时间后执行
+ 延迟执行：任务会在指定的延迟时间之后执行，而不是立即执行
+ 执行一次："Delayed jobs" 是一次性任务，执行完成后不会再次触发
+ 适用场景：适用于那些需要在将来某个时间点执行的任务，例如提醒、定时任务等
+ BackgroundJob.Schedule： 用于创建一个 "Delayed job"
    + 接受一个委托表示要执行的后台任务以及一个 TimeSpan 对象，表示任务要延迟执行的时间

```
var jobId = BackgroundJob.Schedule(() => Console .WriteLine( "Delayed job executed!" ),TimeSpan.FromDays(7));
```

### Recurring

Recurring jobs（定期任务）：按照预定的时间间隔循环执行
+ 定期执行：任务会按照预定的时间间隔循环执行
+ 执行多次："Recurring jobs" 是可以无限次执行的，除非手动停止
+ 适用场景：适用于那些需要定期执行的任务，例如数据清理、定时统计等
+ RecurringJob.AddOrUpdate：用于创建一个 "Recurring job"
    + 接受一个委托表示要执行的后台任务以及一个 Cron 表达式，表示任务的执行时间间隔

```
RecurringJob.AddOrUpdate("Recurring job executed!" ,() => Console .WriteLine( "Recurring job executed!" ),Cron.Daily);
```

### Continuations

Continuations jobs(连续作业) ：任务会在另一个任务完成后自动触发执行
+ 依赖关系：Continuations 作业允许你创建任务之间的依赖关系，确保一个任务完成后，相关的任务会被触发执行
+ 执行一次：Continuations 作业是一次性任务，执行完成后不会再次触发
+ 传递参数：可以通过参数将父作业的结果传递给 Continuations 作业，实现任务之间的信息传递
+ ContinueWith：可以使用 ContinueWith 方法来创建 Continuations 作业。该方法接受一个父作业的标识符（ID）和一个委托，该委托指定了子作业的执行逻辑

```
// 创建父作业
var parentJobId = BackgroundJob.Enqueue(() => 42);

// 创建带有参数的 Continuations 作业
BackgroundJob.ContinueWith<int>(parentJobId, result => Console.WriteLine($"Continuation Job executed with result: {result}"));
```

### Batches

Batches jobs(合并批次作业)：将多个任务组合成批次并一起执行的机制
+ 任务批次：Batches 允许你将多个任务组合成一个批次，以便一起执行
+ 任务依赖关系：可以在批次中设置任务之间的依赖关系，确保任务按照指定的顺序执行
+ 批次状态：可以使用返回的 ID 跟踪批次的状态，检查批次中每个任务的执行情况
+ BatchJob.StartNew：创建批次，并向批次添加任务
    + 允许将一组任务作为单个实体进行管理，可以方便地跟踪批次中每个任务的状态

```
// 创建批次
var batchId = BatchJob.StartNew(batch =>
{
    // 向批次添加任务
    var job1 = batch.Enqueue(() => Console.WriteLine("Job 1 executed!"));
    var job2 = batch.Enqueue(() => Console.WriteLine("Job 2 executed!"));

    // 设置任务之间的依赖关系
    batch.ContinueJobWith(job1, () => Console.WriteLine("Continuation for Job 1 executed!"));
});

// 可以使用 batchId 跟踪批次的状态
Console.WriteLine($"Batch ID: {batchId}");
```

### Batch Continuations

Batch Continuations jobs(批次内连续作业)：允许一个批次中的任务完成后，自动触发另一个任务
+ 任务批次：Batch Continuations 允许你在一个批次中的任务完成后，自动触发另一个任务
+ 任务依赖关系：可以在批次中设置任务之间的依赖关系，确保任务按照指定的顺序执行
+ 批次状态：可以使用返回的 batchId 跟踪批次的状态，检查批次中每个任务的执行情况
+ Batch.ContinueBatchWith：用于在指定的批次完成后执行额外的操作
    + 接受一个回调函数，这个回调函数中可以使用参数添加新的任务到批次中

```
// 创建批次
var batchId = BatchJob.StartNew(batch =>
{
    // 向批次添加任务
    var job1 = batch.Enqueue(() => Console.WriteLine("Job 1 executed!"));
    var job2 = batch.Enqueue(() => Console.WriteLine("Job 2 executed!"));

    // 使用 Batch.ContinueBatchWith 设置 Batch Continuations
    batch.ContinueBatchWith(x =>
    {
        x.Enqueue(() => Console.WriteLine("Batch Continuation executed!"));
        x.Enqueue(() => Console.WriteLine("Additional Job after Batch Continuation"));
    });
});

// 可以使用 batchId 跟踪批次的状态
Console.WriteLine($"Batch ID: {batchId}");
```

### ContinueBatchWith ，ContinueJobWith，ContinueWith三者的区别

ContinueBatchWith：
+ 用于将后台作业添加到批处理中
+ 批处理是一组相关的后台作业，可以按顺序执行，而不考虑前一个作业的成功或失败
+ 适用于在一个批处理中定义一系列相关的作业，这些作业可以同时执行

ContinueJobWith：
+ 用于在一个作业成功完成后触发下一个作业
+ 适用于建立一个顺序执行的作业链，每个作业的触发都依赖于前一个作业的成功完成

ContinueWith：
+ 用于在一个作业无论成功还是失败都触发下一个作业
+ 提供了更灵活的触发条件，可以根据前一个作业的状态来决定是否触发下一个作业

## 后台作业 执行步骤

BackgroundJob.Enqueue()、BackgroundJob.Schedule()、RecurringJob.AddOrUpdate()、BackgroundJob.ContinueWith()

以上方法在创造作业并执行时，都会有以下步骤：
+ 任务定义：定义 即将 在后台  执行 的任务
+ 调用 对应 方法：调用对应方法并将 任务定义去 传递给它，并传递必要的参数
+ 序列化方法信息及其所有参数： 将任务及其参数进行序列化，把任务的执行信息保存在后台存储中，在需要时可还原原始的方法和参数信息
+ 根据序列化信息创建新的后台作业：创建后台作业对象，这个对象包含任务的详细信息和执行任务的依赖关系
+ 将后台作业保存到持久存储中：将创建的后台作业对象保存到持久存储中，确保重启时仍能获得任务信息
+ 将后台作业排队到其队列中：将创建的后台作业排队到 Hangfire 的后台队列，等待后续的执行

执行这些步骤后，该 方法立即返回给调用者

BatchJob.StartNew() 方法在创造作业并执行时，会有以下步骤：
+ 创建批次：调用 BatchJob.StartNew 方法，该方法返回一个代表新批次的对象
+ 先批次添加任务： 使用批次对象，调用 batch.Enqueue 方法向 批次中 添加任务
+ 设置任务之间的依赖关系： 调用 batch.ContinueWith 方法，设置任务之间的依赖关系
+ 获取批次 ID： BatchJob.StartNew 方法返回创建的批次的 ID，可使用ID 跟踪批次的状态
+ 序列化批次信息：将批次信息序列化，以确保在应用程序重启时仍能获取批次信息
+ 将批次信息保存到持久存储中： 将序列化的批次信息保存到Hangfire 的持久存储中
+ 将批次作业排队到其队列中：将创建的批次这样排队到 Hangfire的后台队列，等待后续的执行

Batch.ContinueBatchWith() 方法在创造作业并执行时，会有以下步骤：
+ 创建批次：使用 BatchJob.StartNew 方法 创建一个批次
+ 任务完成后的处理：当批次中的任务完成后，Hangfire 将自动触发 Batch Continuations
+ 调用 Batch.ContinueBatchWith() 方法: 设置在批次中的任务完成后执行的额外操作
+ 序列化任务信息及其所有参数: 将任务及其所有参数序列化，把任务的执行信息保存到后台存储中，需要时还原出原始的方法和参数信息
+ 根据序列化信息创建新的后台作业：使用序列化信息，创建一个新的后台作业对象，并包含任务的详细信息，以及执行任务的依赖关系
+ 将后台作业保存到持久存储中：新创建的后台作业对象保存到 Hangfire 持久存储中，确保重启时仍能获得任务信息
+ 将后台作业排队到其队列中：将创建的后台作业排队到 Hangfire 的后台队列中，等待后续的执行

## 举例

### BackgroundJob.Enqueue 

```
BackgroundJob.Enqueue(() => Console.WriteLine("Hello, world!"));
```

BackgroundJob.Enqueue 是一次性立即调用，会直接调用BackgroundJob.Enqueue方法创建作业并将唯一表示 该作业的 Id 返回回来

例：
在service 层调用，使用BackgroundJob.Enqueue方法，则队列作业中会加入一条的作业，唯一标识这条作业的 ID 会有  BackgroundJob.Enqueue 返回回来

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command, CancellationToken cancellationToken)
{
      string jobId=  BackgroundJob.Enqueue<IPersonDataProvider>(x=>x.CreateAsync(new Person()
         {
             Id = 9,
         }, cancellationToken));
       return new PeopleCreatedEvent
        {
            Result = jobId
        };
}
```

hangfire 的控制面板会有显示出来

![image.png](https://upload-images.jianshu.io/upload_images/29476859-06f082adc0493c8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对应数据库会添加一条Id 为9 的数据

![image.png](https://upload-images.jianshu.io/upload_images/29476859-626d87e17e69fa46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### BackgroundJob.Schedule

```
BackgroundJob.Schedule(() => Console.WriteLine("Hello, world"),TimeSpan.FromDays(1)); // 延长一天后执行Hello,world
```

BackgroundJob.Schedule 是推迟调用，会在间隔时间后调用BackgroundJob.Schedule方法并传递所需的延迟，并将唯一标识该作业的 ID 返回回来

例：
在service 层调用，使用BackgroundJob.Schedule方法，则计划作业中会加入一条的作业，唯一标识这条作业的标识符会有  BackgroundJob.Schedule 返回回来

```
public async Task<PeopleUpdatedEvent> UpdatePersonAsync(UpdatePeopleCommand command, CancellationToken cancellationToken)
{
       var jobId = BackgroundJob.Schedule<IPersonDataProvider>(x =>
       x.UpdatePersonAsync(new Person()
              {
                Id = 9,
                FirstName = "Fang",
                LastName = "chuying"
             },CancellationToken.None),TimeSpan.FromMinutes(1));
     
          return new PeopleUpdatedEvent
        {
            Result = jobId
        };
}
```

一分钟之后，刚刚添加到计划队列的工作会在队列中执行，执行完毕后，对应数据库会更改一条Id 为9 的数据

![image.png](https://upload-images.jianshu.io/upload_images/29476859-92c25782097ce1cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-c4f9883ed91a04d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###  RecurringJob.AddOrUpdate

```
RecurringJob.AddOrUpdate("myrecurringjob", () => Console.Write("Recurring!"), Cron.Daily);
```

RecurringJob.AddOrUpdate 会创建一个新的重复作业或者 使用相同的 ID 更新现有作业

#### RecurringJob.RemoveIfExists

可以通过RecurringJob.RemoveIfExists方法删除现有重复的作业，当没有此类重复作业时，它不会引发异常

```
RecurringJob.RemoveIfExists("some-id");
```

#### RecurringJob.Trigger

周期性作业本身不会记录触发调用的信息，也不会从本次运行中重新计算下次执行时间
若要立即运行重复作业，请调用该RecurringJob.Trigger方法
```
RecurringJob.Trigger("some-id");
```

例：
在service 层调用，使用RecurringJob.AddOrUpdate方法，传入或添加工作作业标识符，已经对应要执行的方法和cancellationToken（传参根据需要以及对应规则传）


```
public async Task UpdatePersonAsync(UpdatePeopleCommand command, CancellationToken cancellationToken)
 {
     RecurringJob.AddOrUpdate<IPersonDataProvider>(
      "29",
       x => x.UpdatePersonAsync(new Person()
      {
          Id = 9,
          FirstName = "Xi",
          LastName = "jinping"
     }, cancellationToken),
     "0 13 11 29 7 *", new RecurringJobOptions()
     {
        TimeZone = TimeZoneInfo.FindSystemTimeZoneById("China Standard Time")
    });
}
```
hangfire 的控制面板中，周期性作业会有更刚刚添加的定期作业，根据需要立即执行或者等待周期执行

![image.png](https://upload-images.jianshu.io/upload_images/29476859-92749dabc81aad6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Hangfire Server

[Hangfire Server](https://docs.hangfire.io/en/latest/background-processing/processing-background-jobs.html) 部分负责后台作业处理，它检查持久存储中是否有排队的后台作业并以可靠的方式执行它们

Hangfire Server 由执行不同工作的不同组件组成，以下是几个重要的：
+ 工作人员侦听队列和处理作业
+ 循环调度程序将循环作业排入队列
+ 调度轮询器将延迟作业排入队列
+ 过期管理器 ：删除过时的作业并尽可能保持存储干净

Hangfire Server 服务器不依赖于 ASP.NET，可以在任何地方启动，从控制台应用程序到 Microsoft Azure Worker Role。所有应用程序的单一 API 通过该类公开BackgroundJobServer：

```
var server = new BackgroundJobServer();
server.Dispose(); // 确保正常关闭功能能 正常工作
```

Hangfire Server 中处理队列作业的基本流程如下：
1. 工作线程池启动
     + Hangfire Server 启动时，会创建一个专用的工作线程池，其中包含多个工作线程（workers）
     + 工作线程 workers 会在后台运行，等待获取并处理队列中的作业

2. 轮询队列
    + 每个工作线程都会定期轮询指定的队列，尝试获取下一个要执行的作业
    + 队列中的作业通常是通过 Hangfire 提供的 API 添加的，如 BackgroundJob.Enqueue、BackgroundJob.Schedule、RecurringJob.AddOrUpdate 等

3. 获取下一个作业
    + 当工作线程发现队列中有待执行的作业时，它会尝试获取作业
    + 作业的获取是原子的，确保每个作业只由一个工作线程获取，避免重复执行

4. 隐藏作业
    + 一旦工作线程成功获取作业，它会将作业标记为隐藏，以防止其他工作线程同时执行相同的作业
    + 这确保了作业的顺序执行和不会被重复执行

5. 执行作业
    + 获取作业后，工作线程会执行作业本身的逻辑，即开发者定义的实际后台任务逻辑
    + 在执行作业前后，Hangfire 允许使用扩展过滤器（extension filters）进行处理，执行额外的逻辑

6. 从队列中删除作业
    + 一旦作业成功执行，工作线程会从队列中删除该作业，确保它不再被其他工作线程执行
    + 如果作业执行失败，Hangfire 会根据配置的重试机制尝试重新执行作业，而不会立即删除作业

7. 重复执行
    + 如果作业执行失败，Hangfire 提供了重试机制，可以根据配置的重试策略尝试重新执行作业
    + 重试机制确保即使作业执行失败，系统也有机会尝试重新执行

8. 循环处理
    + 工作线程会在一个循环中不断地轮询队列、获取作业、执行作业，直到服务器停止或发生异常


因此，只有在处理成功后，作业才会被删除。即使在执行过程中某个进程被终止，Hangfire也会执行补偿逻辑来保证每个作业的处理

# Cron

Cron 是一个用于在 UNIX 系统和类 UNIX 系统（包括 Linux）上运行定期任务的时间调度工具。它通过一系列时间和日期字段来指定任务的运行时间

Cron 表达式通常由五个字段组成，分别表示分钟、小时、日期、月份和星期。每个字段可以包含多个值，也可以使用通配符和范围

```
RecurringJob.AddOrUpdate("powerfuljob", () => Console.Write("Powerful!"), "0 12 * */2");
```

基本书写结构是这样的： 分 时 天  月 周 年

该类`Cron`包含不同的方法和重载，可以按分钟、每小时、每天、每周、每月和每年运行作业。您还可以使用[CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)来指定更复杂的计划：
![image.png](https://upload-images.jianshu.io/upload_images/29476859-21af1fcddb1d9c75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-34939c4858c36a78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以指定每n个时间间隔*/n运行一次

Week 和 Day 存在互斥关系，不可以同时设定规则，如果一个设定规则了，另一个可以使用 ？ 来代表 "都可以"的意思

Hangfire 官网： https://docs.hangfire.io/en/latest/getting-started/index.html

Cron 官网：https://en.wikipedia.org/wiki/Cron#CRON_expression
