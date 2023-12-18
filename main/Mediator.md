# 一、Mediator Pattern——中介者模式

中介者模式（Mediator Pattern）是一种行为型设计模式，它旨在减少对象之间的直接相互通信，而是通过一个中介对象来协调和控制对象之间的交互。这种模式有助于降低系统中对象之间的耦合度，使系统更加可维护和可扩展
中介者模式的结构如下图：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-88ef632eb839c772.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Mediator：抽象中介者角色，定义一个接口，用于其它Colleague进行通信
Colleague：同事接口，与其它同事通信时依靠Mediator进行通信
ConcreteMediator：具体中介者，了解并维护各个Colleague，协调它们的通信行为
ConcreteColleague: 具体同事类，继承自同事类Colleague并实现自己的消息处理逻辑

1. 优点
+ 简化了对象之间的交互，用中介者和同事的一对多交换替代原来的同事之间的多对多交换
+ 可以适当使用中介者模式减少同事之间的过渡耦合，让各同事类之后可以相对独立的使用
+ 可以减少大量同事子类的生成，改变同事行为只需要生成新的中介者子类
+ 将对象的行为和协助进行抽象，灵活处理对象间的相互作用

2. 缺点
+ 过度使用会导致中介者逻辑复杂化，使得控制集中化，将交互的复杂性变成可中介者的复杂性
+ 将多个对象直接的相互依赖转化为中介者和多个同事类的依赖关系，当同事类越多时，中介者会变得复杂且难以维护

# 二、中介者模式与MVC结构

在MVC（Model-View-Controller）结构中，中介者模式可以用于解耦和协调模型（Model）、视图（View）和控制器（Controller）之间的通信。
MVC模式中的通信是通过观察者模式 （Observer Pattern）来实现的。其中视图（View）观察模型（Model）的变化，控制器（Controller）处理用户操作并更新模型（Model)。然而，当应用程序变得复杂时，模型、视图和控制器之间的直接通信可能会导致混乱和紧耦合。这时候，可以引入一个中介者来管理它们之间的通信。
即 Controller层不再需要同Service层（用来封装和处理业务逻辑的）直接交互，只需要在Controller层 引入mediator ，交由mediator 去处理对应的通信

ps: 观察者模式是对象行为模式，它定义对象间的一种一对多的依赖关系，当一个对象(目标对象)的状态发生改变时，所有依赖于它的对象(观察对象)都得到通知并被自动更新。特点：被观察者和观察者一般是一对多的关系，一个被观察者对应多个观察者，当一个被观察者的状态发生改变时，被观察者通知观察者，然后可以在观察者内部进行业务逻辑的处理，如下图：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-805323e931c87d31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中模型（Model）可对应于观察者模式中的观察目标，而视图（View）对应于观察者，控制器（Controller）可充当两者之间的中介者。当模型层的数据发生改变时，视图层将自动改变其显示内容。

# 三、Mediator 的工作原理

Mediator 的主要工作是协调相关对象之间的交互，可以通过注册了Handler（处理者）和信息Message（消息）之间的绑定，在收到特定信息时就会在它的注册表里找出对应的Handler从而调用。
即我们在controller层，我们引入一个mediator，controller发对应的信息（message contract)

```  
[HttpPost]
[Route("create")]
public async Task<IActionResult> CreateAsync([FromBody] CreatePeopleCommand command)
{
    var response = await _mediator.SendAsync<CreatePeopleCommand, CreatePeopleResponse>(command).ConfigureAwait(false);

    return Ok(response);
}
```

在对应的CreatePeopleCommand类中，通过调用SendMessage 来判断对应的 Command，Response类型 

```
public class CreatePeopleCommand : ICommand
{
    public Person person { get; set; }
}

public class CreatePeopleResponse : IResponse
{
    public string result { get; set; }
}
```

进入对应的管道后， 找到对应的Handler类，并调用Handler方法

```
public class CreatePeopleCommandHandler : ICommandHandler<CreatePeopleCommand, CreatePeopleResponse>
{
    private readonly IPersonService _personService;

    public CreatePeopleCommandHandler(IPersonService personService)
    {
        _personService = personService;
    }

    public async Task<CreatePeopleResponse> Handle(IReceiveContext<CreatePeopleCommand> context, CancellationToken cancellationToken)
    {
        var @event = await _personService.AddPersonAsync(context.Message, cancellationToken).ConfigureAwait(false);

        await context.PublishAsync(@event, cancellationToken).ConfigureAwait(false);

        return new CreatePeopleResponse
        {
            Result = @event.Result
        };
    }
}
```

通过调用了 _personService 的 AddPersonAsync 方法后会生成一个事件，并通过PublishAsync通知到对应的 EventHandler去做后续的处理

```
public class PeopleCreatedEvent : IEvent
{
    public string result { get; set; }
}
```

```
public class PeopleCreatedEventHandler : IEventHandler<PeopleCreatedEvent>
{
    public async Task Handle(IReceiveContext<PeopleCreatedEvent> context, CancellationToken cancellationToken)
    {
        await Task.CompletedTask;
    }
}
```

PersonService中调用PersonDataProvider的CreatAsync方法

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command, CancellationToken cancellationToken)
{
    return new PeopleCreatedEvent
    {
        result = await _personDataProvider.CreatAsync(command.person, cancellationToken).ConfigureAwait(false) > 0
            ? "数据写入成功"
            : "数据写入失败"
    };
}
```











