# ProjectUseAutofacDemo:在项目中安装并使用Autofac

## 一. 配置Autofac的插件

打开rider的项目,在对应的项目文件选择  管理NuGet软件包

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d5eabfe738c28ada.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在搜索栏搜索 autofoc,下载安装以下两个软件包(注意版本号)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-a047a276d2460c1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载安装之后,在项目的Program.cs文件配置 Autofac 作为服务提供程序工厂

![image.png](https://upload-images.jianshu.io/upload_images/29476859-8bda4dd737b91cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在项目的Startup.cs 文件通过builder.RegisterModule  注册一个PractiseForFreyaModule 的自定义模块控制器，该模块的构造函数接受应用程序的配置信息和 PractiseForFreyaModule 类型所在的程序集

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d845e4fc16b21fd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用Autofac 作为服务提供程序工厂,项目可以进行更高级的依赖注入和管理

若要为项目引入其他项目作为依赖的话,可以右键项目->添加->引用, 选择需要引入的项目

![image.png](https://upload-images.jianshu.io/upload_images/29476859-bd22e713e4c3ebb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-ee46ed3309968042.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以在项目的.csproj 文件引入中查看到引入的项目依赖

![image.png](https://upload-images.jianshu.io/upload_images/29476859-5a6270cc542b5076.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在,PractiseFirFreya.Api项目就可以引用项目PractiseForFreya.core 作为依赖了

## 二.使用 Autofac 创建IoC容器

1.配置自定义模块控制器的生命周期和 容器注册

在对应的模块控制器中继承 Autofac.Module 模块，Autofac.Module 是 Autofac IoC 容器库中的一个基础类，用于定义模块化的依赖注入配置，Autofac.Module 类中的主要方法是 Load 方法，它是一个virtual方法，用于配置依赖注入。

在自定义的模块类中可以重写 Load 方法 ，模块中注册类型、设置生命周期等,实现对于依赖着的注入

```
public class MyModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        // 在此方法中进行依赖注入的配置
    }
}
```

在配置IoC 容器时，需要进行注册，同时根据需要设置容器的生命周期及类型

使用 typeof(IService).Assembly.GetTypes() 获取 IService 接口所在的程序集中的所有类型，并返回一个 Type 数组

![image.png](https://upload-images.jianshu.io/upload_images/29476859-ea20ea52bb91fd27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过这个模块类，可以自动扫描 IService 接口所在程序集中的所有实现类，并根据不同的接口类型采用不同的注册策略将它们注册到 Autofac 容器中

2. 依赖注入
    + 创建一个接口类文件,用于各类生命周期的创建和继承

![image.png](https://upload-images.jianshu.io/upload_images/29476859-ce16693ced4253b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   + 对象 HelloMessageService 使用构造函数将依赖注入,依赖接口 IHelloWorldDataProvider

    ⚠️ 对象和接口的生命周期不一致

![image.png](https://upload-images.jianshu.io/upload_images/29476859-44f663ec8e2b8b54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    还有使用简便的方式,即通过接口实现依赖注入(接口类文件一样要创建),对象继承了接口所使用的生命周期
    
    ⚠️ 对象与接口的生命周期一致

![image.png](https://upload-images.jianshu.io/upload_images/29476859-759b0751d53bc47a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三.使用 Autofac 创建的 IoC 容器

在需要使用 IoC 容器的项目引入依赖之后

![image.png](https://upload-images.jianshu.io/upload_images/29476859-97b0223d6cbbf86e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在项目的的控制层文件Controller 下创建控制器类的文件, 如下图所示, HelloWorldController 的构造函数使用了依赖注入,通过将 IHelloMessageService 的实现实例注入到控制器，使控制器能
够使用 IHelloMessageService 的功能

[HttpGet] 是定义了一个HTTP GET方法

[Route("api/hello")] 属性来指定该方法的路由, 当访问该路径时,将执行Hello方法

IActionResult 是一个接口，表示一个操作结果，它可以是 HTTP 响应的各种类型

在这种情况下，当Hello 方法返回一个 Ok 结果，即返回 HTTP 状态码为 200（成功）的响应

此时，我们可以启动程序查看运行结果，在对应的项目配置文件launchSettings.json中查看 本地端口号，并拼接上路由 “api/hello”，如下图

![image.png](https://upload-images.jianshu.io/upload_images/29476859-064fbea4bb6d18e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以在https://localhost:7081/api/hello或者 http://localhost:5082/api/hello 中查看运行结果

![image.png](https://upload-images.jianshu.io/upload_images/29476859-e544d8b5cd0f0e8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

⚠️注意：在第一次启动 Rider 项目时,rider 会自动下载相关证书，只要信赖该证书便可以启动程序，若遇到以下的报错信息

```
Unhandled exception.
System.InvalidOperationException: Unable to configure HTTPS endpoint. No server certificate was specified, and the default developer certificate could not be found or is out of date.
To generate a developer certificate run 'dotnet dev-certs https'. To trust the certificate (Windows and macOS only) run 'dotnet dev-certs https --trust'.
```

可以在终端或者控制台执行 “dotnet dev-certs https” 代码，可能会要要求输入本机登陆密码，一般情况下执行成功就可以启动项目
如果执行之后，依旧报以下错误

```
There was an error saving the HTTPS developer certificate to the current user personal certificate store.
```

则需要查看钥匙串中是否存在“登录”模块

![image.png](https://upload-images.jianshu.io/upload_images/29476859-15fd84036dbacc73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

若不存在，则点击 文件添加钥匙串，找到 Keychains 

查看是否有 login.keychain-db 的钥匙串

有则选择后添加该钥匙串，无则查看是否有 login开头的名字钥匙串，一般都是login_renamed_1.keychain-db 这种形式的钥匙串

在访达中重命名该钥匙串为 login.keychain-db 

然后 将刚刚重命名的钥匙串 添加默认钥匙串中，并查看是否存在localhost 证书

存在并可以执行上述终端代码的操作“dotnet dev-certs https” 启动项目，完成之后查看 对应端口路由地址 是否可以正常显示

![image.png](https://upload-images.jianshu.io/upload_images/29476859-e468f1d2f42ee68d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


























