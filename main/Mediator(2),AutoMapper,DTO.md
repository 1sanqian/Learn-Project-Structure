#  一、mediator 的实现过程

Mediator 的主要工作是协调相关对象之间的交互，可以通过注册了Handler（处理者）和信息Message（消息）之间的绑定，在收到特定信息时就会在它的注册表里找出对应的Handler从而调用。

1. 在controller 层，引入mediator, controller层中处理协调和控制对象之间的交互，以下是Post请求的例子

``` 
[ApiController]
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

# 二、AutoMapper

AutoMapper的作用在简化.NET应用程序中的对象映射过程。它提供一种自动化方法将一个对象的属性值复制到另一个对象的对应的属性上，减少手写编写映射代码的工作量

对象映射：AutoMapper使您能够定义对象之间的映射规则，无需手动编写属性赋值代码。根据预定义的规则自动执行属性值的复制，从而实现对象之间的映射，同时实现一个对象的属性值传递给另一个对象。

提高开发效率：通过自动执行对象映射，AutoMapper减少了手动编写大量的赋值代码的工作量，从而提高了开发效率，只需定义一次映射规则，就可重复使用它来执行对象映射，无需每次都手动复制属性。

简化复杂映射：当对象之间的属性名称不一致、类型不匹配或存在嵌套关系时，手动编写映射代码可能变得复杂和繁琐。AutoMapper提供了灵活的配置选项，允许自定义映射规则、类型转换、条件映射等，以满足特定的需求。

支持逆向映射：AutoMapper还支持逆向映射，即从目标对象到源对象的映射。这使得在需要进行双向数据绑定或更新操作时更加方便。

1. 建立映射规则

   将 CreateOrUpdatePeopleDto 映射到 Person 中。

```
public class PeopleMapping : Profile
{
    public PeopleMapping()
    {
        CreateMap<CreateOrUpdatePeopleDto, Person>();
    }
}
```

   记得注册一个AutoMapper
```

private void RegisterAutoMapper(ContainerBuilder builder)
{
    builder.RegisterAutoMapper(typeof(PractiseForFreyaModule).Assembly);
}

```

   
2. 使用映射器执行映射

   _mapper.Map<Person>(command.Person) 是将一个 CreateOrUpdatePeopleDto 的对象类映射到 Person 实体中，从而实现两个对象的转化，CreateOrUpdatePeopleDto 的属性值将会被映射到 Person的属性值中

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command, CancellationToken cancellationToken)
{
        var response =
            await _personDataProvider.CreateAsync(_mapper.Map<Person>(command.Person), cancellationToken)
                .ConfigureAwait(false) > 0
                ? "数据写入成功"
                : "数据写入失败";

        return new PeopleCreatedEvent
        {
            Result = response
        };
}
```
   
# 三、DTO

如果涉及到不同层或组件之间需要传输数据可以采用DTO的方式继续数据传递，不同的上下文之间传递数据，以便通过减少数据传输和提高性能来优化应用程序，本文涉及到从控制层接受post的请求数据，由服务层接收控制层传递的请求并与数据访问层进行交互，所以采用的DTO 进行数据的高效输出

1. DTO 的主要作用：

数据传输：DTO 用于在不同层之间传输数据。它可以将多个相关属性组合成一个对象，方便在不同层之间传递和使用数据

数据封装：DTO 可以封装从数据库、外部服务或其他数据源检索的数据。它可以将这些数据转换为应用程序层或服务层需要的特定格式，从而提供更高层次的抽象和封装

数据筛选和转换：DTO 允许在不同层之间进行数据筛选和转换。例如，您可以定义不同的 DTO 类型来仅包含特定字段，或者对数据进行格式转换、单位转换等操作，以满足不同层的需求

减少网络传输量：通过使用 DTO，您可以仅传输需要的数据字段，而不是将整个领域对象传输到客户端。这可以降低网络传输的数据量，提高性能和效率

版本控制：DTO 还可以用于处理应用程序的版本控制。当应用程序的数据结构发生变化时，您可以通过更新和调整 DTO 来适应这些变化，而不会对其他层产生影响

2. 使用 DTO 

将 CreateOrUpdatePeopleDto 类 作为 集合响应对象 CreatePeopleCommand 的属性 Person 的类型

```
public class CreatePeopleCommand : ICommand
{ 
    public CreateOrUpdatePeopleDto Person { get; set; }
}
```

CreateOrUpdatePeopleDto 可以有它自己的各个属性，如 Id ，FirstName 等等

```
public class CreateOrUpdatePeopleDto
{
    public int Id { get; set; }
    
    public string FirstName { get; set; }
    
    public string LastName { get; set; }
    
    public DateTimeOffset CreateAt { get; set; }
}
```

集合响应对象 CreatePeopleCommand 的属性 Person 将作为一个参数,参与到程序执行中，并将 DTO 组装成一个 Person 的实体类响应对象进，从而进行上下文的操作执行

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command,
        CancellationToken cancellationToken)
{
    // 将 CreateOrUpdatePeopleDto 类型的command.Person 作为参数传递获取响应
     var response = await _personDataProvider.CreateAsync(_mapper.Map<Person>(command.Person), cancellationToken).ConfigureAwait(false) > 0 ? "数据写入成功" : "数据写入失败";

        return new PeopleCreatedEvent
        {
            Result = response
        };
}
```
