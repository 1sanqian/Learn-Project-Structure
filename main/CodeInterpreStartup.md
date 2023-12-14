# 代码解读——Startup.cs文件

## IServiceCollection、IApplicationBuilder和IWebHostEnvironment


*文件位于PractiseForFreya -> Facade -> PractiseForFreya.Api-> Startup.cs*

*文件地址：https://gitlab.sjfood.us/solar/PractiesForFreya/-/blob/master/src/PractiseForFreya.Api/Startup.cs*

### 一. 代码解读

1. 这段代码主要添加 MVC 和 Web API 控制器相关的服务容器中，使得应用程序就可以使用 MVC 架构和 Web API 控制器来处理请求和生成响应

```
public void ConfigureServices(IServiceCollection services)
{
  services.AddMvc();
  services.AddControllers();
}
```

+ services.AddMvc() 是将 MVC（Model-View-Controller）服务添加到服务容器中的方法
+ services.AddControllers() 是将 Web API 控制器服务添加到服务容器中的方法

2. 这段代码的主要作用是应用程序启用开发环境下的异常页面，并配置了路由和终端点处理，以使控制器能够处理请求和生成响应

```
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
     if (env.IsDevelopment())
      {
          app.UseDeveloperExceptionPage();
      }

   app.UseRouting();
   app.UseEndpoints(endpoints => { endpoints.MapControllers(); });
}
 ```    

+  env.IsDevelopment() 若当前应用程序环境处于开发环境则返回 true。
+  app.UseDeveloperExceptionPage() 在开发环境下显示详细的异常信息页面,它能够捕获并显示应用程序中发生的异常
+  app.UseRouting() 用于启用路由功能,根据请求的 URL 路径来决定将请求发送给哪个中间件进行处理
+  app.UseEndpoints() 用于配置终端点路由
+  endpoints.MapControllers() 方法将 HTTP 请求映射到控制器的动作方法，用于处理请求并生成响应

### 二. 知识点解读

1. ConfigureServices

   ConfigureServices 方法是一个在启动时由框架自动调用的方法，用于配置应用程序的服务容器
   IServiceCollection 是一个用于注册和管理应用程序服务的集合
    + 构建对象实例
    + 配置选项
    + 依赖注入

2. services.AddMvc（）

    services.AddMvc() 是将 MVC（Model-View-Controller）服务添加到服务容器中的方法

    它会注册必要的服务和中间件，以支持 MVC 架构模式

    添加 MVC 服务后，应用程序可以使用控制器、视图和模型来处理请求和生成响应

3. services.AddControllers()

    services.AddControllers() 是将 Web API 控制器服务添加到服务容器中的方法

    它会注册 Web API 控制器所需的服务和中间件

   添加 Web API 控制器服务后，应用程序可以使用控制器来处理 Web API 请求

4. IServiceCollection

    IServiceCollection 提供了一组方法，用于向服务容器中注册和配置应用程序的服务
    + AddTransient<TService, TImplementation>()：将指定类型的服务注册为瞬时（transient）生命周期的服务，每次请求都创建一个新的实例。
    + AddScoped<TService, TImplementation>()：将指定类型的服务注册为作用域（scoped）生命周期的服务，每个请求期间都会使用同一个实例。
    + AddSingleton<TService, TImplementation>()：将指定类型的服务注册为单例（singleton）生命周期的服务，整个应用程序只会创建一个实例。
    + AddTransient<TService>()：将指定类型的服务注册为瞬时生命周期的服务，并使用自身作为服务的实现类型
    + AddScoped<TService>()：将指定类型的服务注册为作用域生命周期的服务，并使用自身作为服务的实现类型
    + AddSingleton<TService>()：将指定类型的服务注册为单例生命周期的服务，并使用自身作为服务的实现类型
    + AddTransient()、AddScoped() 和 AddSingleton() 方法还有其他重载，可以根据需要传递不同的参数来注册服务

5. IApplicationBuilder

   IApplicationBuilder 提供了一组方法，用于向请求处理管道中添加中间件、配置中间件的顺序以及其他与请求处理相关的操作
   + Use()：用于添加自定义的中间件到请求处理管道中
   + UseMiddleware<TMiddleware>()：用于将指定的中间件类型添加到请求处理管道中
   + UseMiddleware<TMiddleware>(params object[] args)：用于将指定的中间件类型添加到请求处理管道中，并传递参数给中间件的构造函数
   + UseRouting()：用于启用路由功能
   + UseEndpoints()：用于配置终端点路由
   + UseStaticFiles()：用于启用静态文件服务
   + UseAuthentication()：用于启用身份验证中间件
   + UseAuthorization()：用于启用授权中间件

6. IWebHostEnvironment

   IWebHostEnvironment 提供了访问和操作与 Web 主机环境相关的信息和属性的方法
   + EnvironmentName：获取当前应用程序的环境名称，例如 "Development"、"Staging"、"Production" 等
   + ContentRootPath：获取应用程序的内容根目录的物理路径
   + WebRootPath：获取应用程序的 Web 根目录的物理路径
   + IsDevelopment()：判断当前应用程序是否处于开发环境
   + IsStaging()：判断当前应用程序是否处于暂存环境
   + IsProduction()：判断当前应用程序是否处于生产环境

ps:本文知识点解读存在一定局限性