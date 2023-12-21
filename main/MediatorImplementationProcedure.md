####  一、mediator 的实现过程

Mediator 的主要工作是协调相关对象之间的交互，可以通过注册了Handler（处理者）和信息Message（消息）之间的绑定，在收到特定信息时就会在它的注册表里找出对应的Handler从而调用。

1. 在controller 层，引入mediator, controller层中处理协调和控制对象之间的交互，以下是Post请求的例子

``` 
[ApiController]å
[Route("api/people")]
public class PeopleController : ControllerBase
{
    private readonly IMediator _mediator;

    public PeopleController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpPost]
    [Route("create")]
    public async Task<IActionResult> CreateAsync([FromBody] CreatePeopleCommand command)
    {
        var response = await _mediator.SendAsync<CreatePeopleCommand, CreatePeopleResponse>(command).ConfigureAwait(false);

         return Ok(response);
    }

   [HttpPost]
   [Route("update")]
   public async Task<IActionResult> UpdateAsync([FromBody] UpdatePeopleCommand command)
   {
       var response = await _mediator.SendAsync<UpdatePeopleCommand, UpdatePeopleResponse>(command).ConfigureAwait(false);

       return Ok(response);
   }
}
```

由于_mediator.SendAsync 是 Mediator.Net 包自主分装的函数，所以必须按照给定的方法和类型去使用
 
```
//SendAsync 的方法规范， Post 请求的两个参数类型是ICommand，IResponse，Get请求的两个参数类型是IRequest，IResponse,对应的方法会有时不同
Task<TResponse> SendAsync<TMessage, TResponse>( 
      TMessage cmd,  
      CancellationToken cancellationToken = default (CancellationToken))
      where TMessage : ICommand    // TMessage参数类型必须是ICommand或者继承ICommand
      where TResponse : IResponse;   //TResponse参数类型必须是IResponse或者继承IResponse
```

所以 CreatePeopleCommand，UpdatePeopleCommand类型必须是ICommand或者继承ICommand，
CreatePeopleResponse，UpdatePeopleCommand类型必须是IResponse或者继承IResponse

```
public class CreatePeopleCommand : ICommand
{
    public CreateOrUpdatePeopleDto Person { get; set; }
}

public class CreatePeopleResponse: IResponse
{
    public string Result { get; set; }
}

public class CreatePeopleResponse<T>: IResponse
{
    public T Result { get; set; }
}
```

在controller层引入mediator 后，可以是Handler 帮忙处理Message的
在 对应的 handler 文件中，去调用对应的Service 函数执行功能 同时会返回一个 response 

```
namespace PractiseForFreya.Core.Handler.CommandHandler;

public class CreatePeopleCommandHandler : ICommandHandler<CreatePeopleCommand, CreatePeopleResponse>
{
    private readonly IPersonService _personService;

    public CreatePeopleCommandHandler(IPersonService personService)
    {
        _personService = personService;
    }

    public async Task<CreatePeopleResponse> Handle(IReceiveContext<CreatePeopleCommand> context, CancellationToken cancellationToken)
    {
        var @event = await _personService.AddPersonAsync(context.Message, cancellationToken).ConfigureAwait(false);  // service 层函数 AddPersonAsync 可以返回一个IEvent 类型的事件，
       
        await context.PublishAsync(@event, cancellationToken).ConfigureAwait(false);
        // 返回的事件若是 IEvent 类型必须使用PublishAsync() 将事件 @event 发布到消息队列中，用于将事件发布到消息中间件（例如消息队列），以便其他订阅者可以接收并处理该事件，即 Event 有它自己的handler 事件去处理
        return new CreatePeopleResponse
        {
            Result = @event.Result
        };
    }
}
```

所以在Service 层要提前定义好要处理和执行的事件 ，一般来说，service文件是 对具体的操作类进行预处理和操作类执行后逻辑的处理，并不具体的去执行特定的事件。而Dataprovider 文件 则是进行具体的动作，例如发送消息，创建或更新数据 等等

```
public class PersonService : IPersonService
{
    private readonly IMapper _mapper;
    private readonly IPersonDataProvider _personDataProvider;

    public PersonService(
        IMapper mapper,
        IPersonDataProvider personDataProvider)
    {
        _mapper = mapper;
        _personDataProvider = personDataProvider;
    }

    public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command,
        CancellationToken cancellationToken)
    {
      // 对具体的操作类执行之后做一个逻辑处理，也可以在具体操作类执行之前对数据做预处理
        var response =
            await _personDataProvider.CreateAsync(_mapper.Map<Person>(command.Person), cancellationToken)
                .ConfigureAwait(false) > 0
                ? "数据写入成功"
                : "数据写入失败";
       // 之后操作类成功与否都会返回一个结果给 Event,通过结合上下文知道Event可以有它自己的handler事件去处理，PeopleCreatedEvent 在接收到这个返回值之后，会去执行 对应的handler 类PeopleCreatedEventHandler
        return new PeopleCreatedEvent
        {
            Result = response
        };
    }

    public async Task<PeopleUpdatedEvent> UpdatePersonAsync(UpdatePeopleCommand command,
        CancellationToken cancellationToken)
    {
        var response =
            await _personDataProvider.UpdatePersonAsync(_mapper.Map<Person>(command.Person), cancellationToken)
                .ConfigureAwait(false) > 0
                ? "更改成功"
                : "更改失败";

        return new PeopleUpdatedEvent
        {
            Result = response
        };
    }
}
```

Dataprovider 文件进行执行的操作，并将执行结果返回给 service 文件

```
public class PersonDataProvider : IPersonDataProvider
{
    private readonly PractiseForFreyaDbContext _dbContext;

    public PersonDataProvider(PractiseForFreyaDbContext dbContext)
    {
        _dbContext = dbContext;
    }
    
    public async Task<int> CreateAsync(Person person, CancellationToken cancellationToken)
    {
       // 创建增加一条数据到数据库中
       await _dbContext.AddAsync(person, cancellationToken).ConfigureAwait(false);
       //保存上面的对数据库操作并返回成功与否的数值（数值>0：成功，数值=0 ：成功但无受影响行数，数值<0：失败）
       return await _dbContext.SaveChangesAsync(cancellationToken).ConfigureAwait(false);
    }

    public async Task<int> UpdatePersonAsync(Person person, CancellationToken cancellationToken)
    {
        // 创建增加一条数据到数据库中
         _dbContext.Update(person);

        return await _dbContext.SaveChangesAsync(cancellationToken).ConfigureAwait(false);
    }
}
```

PeopleCreatedEvent 在接收到返回值之后,返回告诉程序已完成任务

```
public class PeopleCreatedEventHandler : IEventHandler<PeopleCreatedEvent>
{
    public async Task Handle(IReceiveContext<PeopleCreatedEvent> context, CancellationToken cancellationToken)
    {
        await Task.CompletedTask;
    }
}
```

在上述操作之前要提前定义好 command ，event ,response 的基本类定义

```
public class CreatePeopleCommand : ICommand
{ 
    public CreateOrUpdatePeopleDto Person { get; set; }
}

public class CreatePeopleResponse : IResponse
{
    public string Result { get; set; }
}

public class PeopleCreatedEvent : IEvent
{
    public string Result { get; set; }
}
```

若要与数据库交互，还需要提前定义好数据表

```
[Table("people")]
public class Person : IEntity
{
    [Key]
    [Column("id")]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public int Id { get; set; }
    
    [Column("first_name")]
    public string FirstName { get; set; }
    
    [Column("last_name")]
    public string LastName { get; set; }
    
    [Column("create_at")]
    public DateTimeOffset CreateAt { get; set; }
}
```

记得要先注册好 Mediator 和 DbContext 方便进行使用

```

public class PractiseForFreyaModule : Module
{
    private readonly Assembly[] _assemblies;
    private readonly IConfiguration _configuration;
    
    public PractiseForFreyaModule(IConfiguration configuration,params Assembly[] assemblies)
    {
        _assemblies = assemblies;
        _configuration = configuration;
    }
    
    protected override void Load(ContainerBuilder builder)
    {
        //注册Mediator函数
        RegisterMediator(builder);
        
        //注册生命周期函数
        RegisterDependency(builder);
        
        //注册数据库函数
        RegisterDbContext(builder);
        
        //注册当前模块
        RegisterSettings(builder);
        
        //注册AutoMapper
        RegisterAutoMapper(builder);
        
        //注册注册一个类型映射
        RegisterIUnitOfWork(builder);
    }

    private void RegisterDependency(ContainerBuilder builder)
    {
        foreach (var type in typeof(IService).Assembly.GetTypes()
                     .Where(t => typeof(IService).IsAssignableFrom(t) && t.IsClass))
        {
            switch (true)
            {
                case bool _ when typeof(IScopedService).IsAssignableFrom(type):
                    builder.RegisterType(type).AsImplementedInterfaces().InstancePerLifetimeScope();
                    break;
                case bool _ when typeof(ISingletonService).IsAssignableFrom(type):
                    builder.RegisterType(type).AsImplementedInterfaces().SingleInstance();
                    break;
                default:
                    builder.RegisterType(type).AsImplementedInterfaces();
                    break;
            }
        }
    }
    
    private void RegisterMediator(ContainerBuilder builder)
    {
        var mediatorBuilder = new MediatorBuilder();
        
        mediatorBuilder.RegisterHandlers(_assemblies);
        mediatorBuilder.ConfigureGlobalReceivePipe(c =>
        {
            c.UseUnitOfWork();
        });
        
        builder.RegisterMediator(mediatorBuilder);
    }

    private void RegisterIUnitOfWork(ContainerBuilder builder)
    {
        builder.RegisterType<PractiseForFreyaDbContext>().As<IUnitOfWork>();
    }

    private void RegisterSettings(ContainerBuilder builder)
    {
        var settingTypes = typeof(PractiseForFreyaModule).Assembly.GetTypes()å
            .Where(t => t.IsClass && typeof(IConfigurationSetting).IsAssignableFrom(t))
            .ToArray();
    
        builder.RegisterTypes(settingTypes).AsSelf().SingleInstance();
    }
    
    private void RegisterAutoMapper(ContainerBuilder builder)
    {
        builder.RegisterAutoMapper(typeof(PractiseForFreyaModule).Assembly);
    }
    
    private void RegisterDbContext(ContainerBuilder builder)
    {
        builder.RegisterType<PractiseForFreyaDbContext>()
            .AsSelf()
            .As<DbContext>()
            .WithParameter((pi, ctx) => pi.ParameterType == typeof(ConnectionString), 
                (pi, ctx) => ctx.Resolve<ConnectionString>())
            .InstancePerLifetimeScope();

        builder.RegisterType<EfRepository>().As<IRepository>().InstancePerLifetimeScope();
    }
}
```

Ps: 如果需要用mediator执行Get方法，则需要将对应的command改为对应的request，要注意，Get在执行完具体的操作类之后，一般不需要返回Event 事件

####  二、DTO

如果涉及到不同层或组件之间需要传输数据可以采用DTO的方式继续数据传递，不同的上下文之间传递数据，以便通过减少数据传输和提高性能来优化应用程序，本文涉及到从控制层接受post的请求数据，由服务层接收控制层传递的请求并与数据访问层进行交互，所以采用的DTO 进行数据的高效输出

```
public class CreateOrUpdatePeopleDto
{
    public int Id { get; set; }
    
    public string FirstName { get; set; }
    
    public string LastName { get; set; }
    
    public DateTimeOffset CreateAt { get; set; }
}
```
