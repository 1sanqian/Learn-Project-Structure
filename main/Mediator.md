# 一、Mediator Pattern——中介者模式

中介者模式（Mediator Pattern）是一种行为型设计模式，它旨在减少对象之间的直接相互通信，而是通过一个中介对象来协调和控制对象之间的交互。这种模式有助于降低系统中对象之间的耦合度，使系统更加可维护和可扩展。

Mediator的工作原理是它注册了Handler和信息（Message）之间的绑定，因此当他收到特定信息时就会在它的注册表里找出对应的Handler从而调用。

传统模式：controller 层作为协调者，在收到请求后去引入不同的类并调用方法达到目标，大量的代码类紧紧的依赖controller层，使得controller层变得庞大且难以控制

mediator: controller 层不需要再引入不同的类以达成目标，只需引入mediator , 由mediator 把请求发送到目的第，在由目的地对应把类发回来，原先集中在controller 层引入的类，被对应的分散进各个功能handler 中，并交由handler 去处理上下文逻辑，这样，让功能逻辑独立单一出来。由此，controller 只需要负责的对应消息的发送。

中介者模式的结构如下图：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-88ef632eb839c772.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Mediator：抽象中介者角色，定义一个接口，用于其它Colleague进行通信

Colleague：同事接口，与其它同事通信时依靠Mediator进行通信

ConcreteMediator：具体中介者，了解并维护各个Colleague，协调它们的通信行为

ConcreteColleague: 具体同事类，继承自同事类Colleague并实现自己的消息处理逻辑

+ mediator的基本工作流

一个mediator 可以发送多个sendAsync

一个handler 可以接收处理多个 command

一个event 可以发送处理多个command

event 可以自己的EventHanlder ,这个EventHanlder 也可以指向给 event 的上一级handler，构成环形结构

![image.png](https://upload-images.jianshu.io/upload_images/29476859-06ad3f2c6aafa530.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 优点
+ 简化了对象之间的交互，由原先同事之间直接的多对多关系变为同事之间借用中介者的这种间接的多对多的关系
+ 可以适当使用中介者模式减少同事之间的过渡耦合，让各同事类之后可以相对独立的使用
+ 可以减少大量同事子类的生成，改变同事行为只需要生成新的中介者子类
+ 将对象的行为和协助进行抽象，灵活处理对象间的相互作用

2. 缺点
+ 过度使用会导致中介者逻辑复杂化，使得控制集中化，将交互的复杂性变成可中介者的复杂性
+ 将多个对象直接的相互依赖转化为中介者和多个同事类的依赖关系，当同事类越多时，中介者会变得复杂且难以维护

# 二、管道

在Mediator 发送一个消息到Handler之前，如果需要出来一些操作但并不与该消息相关联的一些处理时，可以使用中间件或者Middleware l来处理

![image.png](https://upload-images.jianshu.io/upload_images/29476859-dd6ff787ce60020d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

global: 全局接收管道,每当消息在到达下一个管道和处理程序之前发送、发布或请求时，都会触发此管道

command: 命令接收管道，主要是在消息到达命令处理程序之前和之后触发

event: 事件接收管道，主要是在消息到达事件处理程序之后和之前触发

requerst: 请求接收管道，主要是在请求处理程序之后和之前触发

publish: 发布管道,当在处理程序中发布时，将触发此管道IEvent，此管道仅用于IEvent且通常用作传出拦截器

.net有它自己的管道，mediator的管道是在.net管道的里面，而上述管道是在mediator管道里面的。

# 三、中介者模式与MVC结构

在MVC（Model-View-Controller）结构中，中介者模式可以用于解耦和协调模型（Model）、视图（View）和控制器（Controller）之间的通信。
MVC模式中的通信是通过观察者模式 （Observer Pattern）来实现的。其中视图（View）观察模型（Model）的变化，控制器（Controller）处理用户操作并更新模型（Model)。然而，当应用程序变得复杂时，模型、视图和控制器之间的直接通信可能会导致混乱和紧耦合。这时候，可以引入一个中介者来管理它们之间的通信。
即 Controller层不再需要同Service层（用来封装和处理业务逻辑的）直接交互，只需要在Controller层 引入mediator ，交由mediator 去处理对应的通信

ps: 观察者模式是对象行为模式，它定义对象间的一种一对多的依赖关系，当一个对象(目标对象)的状态发生改变时，所有依赖于它的对象(观察对象)都得到通知并被自动更新。特点：被观察者和观察者一般是一对多的关系，一个被观察者对应多个观察者，当一个被观察者的状态发生改变时，被观察者通知观察者，然后可以在观察者内部进行业务逻辑的处理，如下图：

![image.png](https://upload-images.jianshu.io/upload_images/29476859-805323e931c87d31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中模型（Model）可对应于观察者模式中的观察目标，而视图（View）对应于观察者，控制器（Controller）可充当两者之间的中介者。当模型层的数据发生改变时，视图层将自动改变其显示内容。

ps：有关mediator和管道详细可参考：

什么是Mediator，如何用好它 ：https://www.zhihu.com/column/p/24946643

.Net 的简单中介，用于发送命令、发布事件和请求响应，并支持管道：https://github.com/mayuanyang/Mediator.Net
