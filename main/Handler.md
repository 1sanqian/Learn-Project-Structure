# 一、Handler

Handler 是消息控制机制，主要用于线程间的通信，Handler的通信方案就是共享，Handler 可以从子线程向主线程发送消息，也可以从主线程往子线程发送消息，子线程、Handler、主线程构成了一个消费者-生产者模式

![image.png](https://upload-images.jianshu.io/upload_images/29476859-3672dea7fd1514c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1. Handler的相关类

`Message`是消息实体：里面存储这线程消息

`MessageQueue`是一个由[单链表](https://so.csdn.net/so/search?q=%E5%8D%95%E9%93%BE%E8%A1%A8&spm=1001.2101.3001.7020)实现的优先级消息队列

`Handler`是消息处理类，可以发送消息、获取消息、处理消息、移除消息，子类实现这个方法来处理从队列中取出的消息，使用sendMessage() 发送消息；在handleMessage()中处理

Looper 是消息循环器，负责从MessageQueue中循环取消息，再将取出的消息分发给 handleMessage()，来处理消息。每个线程只能有一个Looper (它是通过ThreadLocal实现的唯一性) ，即多个子线程可以借助同一个Handler进行通信

#### 2. 在不同的上下文中，"handler" 可以有不同的含义。以下是一些常见的使用场景：

请求处理程序（Request Handler）：在Web开发中，请求处理程序是负责接收和处理HTTP请求的组件。它们接受请求、执行相应的操作，并返回响应给客户端。请求处理程序可以是处理特定路由的控制器、中间件或其他类。

事件处理程序（Event Handler）：事件处理程序用于处理应用程序中的事件。当特定事件发生时，事件处理程序会执行相应的操作。事件可以是用户操作、系统事件或其他自定义事件。事件处理程序负责响应事件并执行相应的逻辑。

命令处理程序（Command Handler）：命令处理程序是用于执行特定命令的组件。命令可以是应用程序内部的指令，例如修改数据、触发业务流程等。命令处理程序接收命令，并根据命令的内容执行相应的操作。

消息处理程序（Message Handler）：消息处理程序用于处理消息传递系统中的消息。它们接收消息，并根据消息的类型和内容执行相应的操作。消息可以是在分布式系统中进行通信的一种方式，例如消息队列、事件总线等。

#### 3. Handler机制的工作流程

Handler初始化： 在主线程中创建Looper、MessageQueue、Handler，创建完成后，Looper则自动进入消息循环状态 。同时，Handler自动绑定Looper和MessageQueue。如果消息队列为空，则线程阻塞。主线程中由于在ActivityThread的main()方法中已经初始化过了，所以就不需要在初始化Looper和MessageQueue了。

子线程发送消息：子线程中通过Handler向消息队列MessageQueue中发送消息。

消息循环Looper 循环取出MessageQueue中的Message消息。Looper 将循环取出的消息分发给Handler中的handleMessage()方法。

接收处理消息：    在handleMessage(Message msg)方法中处理消息。

#### 4. Looper的作用

创建Looper对象
创建MessageQueue对象
让Looper对象持有当前线程
Looper相关方法：
Looper.prepare()———为当前线程创建一个Looper；
Looper.loop() ——— 开启消息循环；
Looper.prepareMainLooper() ——— 为主线程创建Looper时使用，在ActivityThread有用到。
Looper.getMainLooper() ——— 通过该方法可以获取主线程的Looper。
Looper.quit() ——— 退出Looper循环。
Looper.quitSafely() ——— 自己创建的Looper，在不使用的时候，需要退出。

#### 5. Thread、Handler、Looper的对应关系

一个Thread线程只能绑定到一个Looper循环器上，但可以有多个Handler实例处理者。
一个Looper循环器可以绑定多个Handler实例。比如主线程的main()方法中创建了Looper和Handler对象，但是我们开发过程中仍然可以创建其他Handler对象。
一个Handler对象处理者，只能绑定到一个Looper循环器中。

ps :本文部分知识点来自网上，原文链接：https://blog.csdn.net/m0_49508485/article/details/127998204
