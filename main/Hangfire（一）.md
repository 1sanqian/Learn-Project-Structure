# 一、Hangfire

Hangfire 提供一种简单而可靠的方法来处理异步任务，定时任务和长时间运行的任务，且允许在后台执行任务，不会阻塞应用程序的主线程(主要用来处理耗时的操作、异步和队列)

Hangfire 使用持久化存储来保持任务的状态和调度信息。它支持多种存储后端，如 SQL Server、MySQL、Redis 等，可以根据您的需求选择合适的存储解决方案

1. 配置包，Hangfire 主要需要下载以下包文件

![image.png](https://upload-images.jianshu.io/upload_images/29476859-6c6aef8472cd83cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 创建 Hangfire 的类（记得放在Hangfire的文件中）

```
public static class HangFireExtension
{
  public static void AddHangFireService(this IServiceCollection services, IConfiguration configuration)
    {

     // 使用 AddHangfire 方法来配置 Hangfire
       services.AddHangfire(config => config

            .SetDataCompatibilityLevel(CompatibilityLevel.Version_170)  //设置Hangfire 存储的数据兼容性级别为 Version_170

            .UseSimpleAssemblyNameTypeSerializer()  //使用简单的程序集名称类型序列化器。这是一种用于序列化和反序列化 Hangfire 任务数据的方法

            .UseRecommendedSerializerSettings()  //使用推荐的序列化器设置。这将使用推荐的序列化器选项进行配置

            .UseStorage(

                new MySqlStorage(  // 使用 MySqlStorage 作为 Hangfire 的存储方式。在这里，创建了一个新的 MySqlStorage 实例，并传递了相关的配置选项

                    configuration.GetConnectionString("Default"),   //从配置中获取名为 "Default" 的连接字符串。这里假设在配置文件中定义了名为 "Default" 的连接字符串用于连接到 MySQL 数据库

                    new MySqlStorageOptions
                    {
                        QueuePollInterval = TimeSpan.FromSeconds(10),  //队列轮询间隔

                        JobExpirationCheckInterval = TimeSpan.FromHours(1),  //作业过期检查间隔

                        CountersAggregateInterval = TimeSpan.FromMinutes(5),   //计数器聚合间隔

                        PrepareSchemaIfNecessary = true,  // 这将在需要时自动准备 Hangfire 数据库模式。如果数据库模式不存在，Hangfire 将自动创建所需的表和架构 

                        DashboardJobListLimit = 25000,  // 这指定了在 Hangfire 仪表板上显示的作业列表的最大数量。在仪表板上，您将能够查看最多 25000 个作业的相关信息

                        TransactionTimeout = TimeSpan.FromMinutes(1), //这是事务超时的设置，用于指定 Hangfire 在执行作业时的事务超时时间

                        TablesPrefix = "Hangfire"  //这将为 Hangfire 数据库表添加一个前缀，以避免与其他表名冲突。在这种情况下，所有的 Hangfire 表都将以 "Hangfire_" 作为前缀。
                    }
                )
            ));

        // 将 Hangfire 作为 IHostedService 添加到服务集合中。这将在应用程序启动时启动 Hangfire 任务处理服务器，并设置工作线程数为 1
        services.AddHangfireServer(options => options.WorkerCount = 1);
    }
}
```

3. 在 Startup.cs 文件中启动Hangfire

```
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc();
     services.AddControllers();
     services.AddHangFireService(Configuration);  // 用于配置和注册 Hangfire 相关的服务
}
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
     if (env.IsDevelopment())
     {
         app.UseDeveloperExceptionPage();
     }
        
     app.UseRouting();
     app.UseHangfireServer();   // 用于将 Hangfire 任务处理服务器添加到请求处理管道中
     app.UseHangfireDashboard();    // 将 Hangfire 仪表板添加到应用程序的请求处理管道中
     app.UseEndpoints(endpoints => { endpoints.MapControllers(); }); 
}
```

运行项目后可以看见数据库自动生成的许多 hangfire 相关的表，这些表都是hangfire用来执行持久化任务

![image.png](https://upload-images.jianshu.io/upload_images/29476859-7f0ef50ee43a1ea0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开项目路由到 hangfire 路径，可以看见hangfire 的仪表盘

![image.png](https://upload-images.jianshu.io/upload_images/29476859-9cedd83ac428f731.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

尚未开始使用hangfire时，仪表盘时空的

# 二、Hangfrie 的调用方法

1. Enqueue

```
BackgroundJob.Enqueue(() => Console.WriteLine("Hello, world!"));
```

该Enqueue方法不会立即调用目标方法，而是运行以下步骤：
+ 序列化方法信息及其所有参数
+ 根据序列化信息创建新的后台作业
+ 将后台作业保存到持久存储中
+ 将后台作业排队到其队列中

执行这些步骤后，该`BackgroundJob.Enqueue`方法立即返回给调用者。

举例

在service 层调用，使用BackgroundJob.Enqueue方法，则队列作业中会加入一条的作业，唯一标识这条作业的标识符会有  BackgroundJob.Enqueue 返回回来

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


2.Schedule

```
BackgroundJob.Schedule(() => Console.WriteLine("Hello, world"),TimeSpan.FromDays(1)); // 延长一天后执行Hello,world
```

Schedule 是推迟调用，会在间隔时间后调用BackgroundJob.Schedule方法并传递所需的延迟

举例

在service 层调用，使用BackgroundJob.Schedule方法，则计划作业中会加入一条的作业，唯一标识这条作业的标识符会有  BackgroundJob.Enqueue 返回回来

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


3. AddOrUpdate

```
RecurringJob.("easyjob", () => Console.Write("Easy!"), Cron.Daily);
```

AddOrUpdate 会创建一个新的重复作业或者 使用相同的 id ("easyjob")更新现有作业

可以通过RemoveIfExists方法删除现有重复的作业，当没有此类重复作业时，它不会引发异常

```
RecurringJob.RemoveIfExists("some-id");
```

周期性作业本身不会记录触发调用的信息，也不会从本次运行中重新计算下次执行时间

若要立即运行重复作业，请调用该Trigger方法

```
RecurringJob.Trigger("some-id");
```

举例

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


**关于 Corn**

```
RecurringJob.AddOrUpdate("powerfuljob", () => Console.Write("Powerful!"), "0 12 * */2");
```

基本书写结构是这样的： 分 时 天  月 周 年

该类`Cron`包含不同的方法和重载，可以按分钟、每小时、每天、每周、每月和每年运行作业。您还可以使用[CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)来指定更复杂的计划：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-21af1fcddb1d9c75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-34939c4858c36a78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以指定每n个时间间隔*/n运行一次

Week 和 Day 存在互斥关系，不可以同时设定规则，如果一个设定规则了，另一个可以使用 ？ 来代表 :"都可以"的意思

# 三、Hangfire Server


另一个 Hangfire 组件称为[Hangfire Server](https://docs.hangfire.io/en/latest/background-processing/processing-background-jobs.html)，它检查持久存储中是否有排队的后台作业并以可靠的方式执行它们。

排队的作业由专用的工作线程池处理。每个worker都会调用以下流程：
1.  获取下一份工作并对其他工人隐藏
2.  执行作业及其所有扩展过滤器
3.  从队列中删除作业。

因此，只有在处理成功后，作业才会被删除。即使在执行过程中某个进程被终止，Hangfire也会执行补偿逻辑来保证每个作业的处理。

Redis存储对于每个步骤和补偿逻辑机制的实现：
*   **Redis**实现使用阻塞`BRPOPLPUSH`命令，因此可以立即获取作业。但如果进程终止，它们仅在超时到期（默认为 30 分钟）后才会重新排队。


ps:

Hangfire 官网： https://docs.hangfire.io/en/latest/getting-started/index.html

Cron 官网：https://en.wikipedia.org/wiki/Cron#CRON_expression
