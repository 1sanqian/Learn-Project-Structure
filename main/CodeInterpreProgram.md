# 代码解读——Program.cs文件

## ConfigurationBuilder和Host


*文件位于PractiseForFreya -> Facade -> PractiseForFreya.Api-> Program.cs*

*文件地址：https://gitlab.sjfood.us/solar/PractiesForFreya/-/blob/master/src/PractiseForFreya.Api/Program.cs*

### 一. 代码解读

1. 这段代码主要用 ConfigurationBuilder 构建一个实例对象 configuration
       
       var configuration = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddEnvironmentVariables()
            .Build();


   + new ConfigurationBuilder() 创建该类型实例对象
   + AddJsonFile（"appsettings.json"）是给 configuration 配置一个 名为 “appsettings.json” 的配置文件
   + AddEnvironmentVariables() 是向这个实例对象添加 环境变量 作为配置源
   + Build() 执行以上 配置构建 操作

2. 这段代码的主要作用是创建一个应用程序的主机环境，并配置了 Web 主机的默认设置，包括加载通用配置、配置日志记录器、配置依赖注入容器，以及指定了一个 Startup 类作为应用程序的启动类

       public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
             .ConfigureWebHostDefaults(webBuilder  => 
             { webBuilder.UseStartup<Startup>(); });

+  这个方法最终会返回一个 IHostBuilder 实例， 即 应用程序的主机环境构建器
+  Host 类 用一种统一的方式启动，停止和管理应用程序的生命周期
+  CreateDefaultBuilder（args）会自动加载通用的基本配置，和依赖注入，并返回一个默认的 实例对象
+  ConfigureWebHostDefaults 是对 web 主机的默认配置进行配置，即改用  Startup 作为启动类 并在启动应用程序时 自动 运行启动类 的配置方法
+  UseStartup<Startup>() 指定了启动类，并在启动应用程序时自动运行启动类的配置方法

### 二. 知识点解读

1. ConfigurationBuilder

   ConfigurationBuilder 主要用于构建和配置不同类型的对象、服务或组件的实例。它提供了一种流畅的API，可以通过链式调用方法来设置各种属性和选项
    + 构建对象实例
    + 配置选项
    + 依赖注入

2. AddJsonFile

   AddJsonFile("appsettings.json")：向 ConfigurationBuilder 添加一个 JSON 配置文件作为配置源


3. appsettings.json

   appsettings.json通常包含应用程序的配置信息，例如数据库连接字符串、日志级别等 


4. AddEnvironmentVariables

   AddEnvironmentVariables() 是 ConfigurationBuilder 类中的一个方法，用于将环境变量添加为配置源。
    

5. Build()

   执行配置构建操作，将添加的配置源整合成一个，最终返回一个 对应数据类型 实例配置对象


6. Host 

   Host类 用于创建和管理应用程序的主机环境

   提供了一种统一的方式来启动、停止和管理应用程序的生命周期

   + 用程序的启动和初始化
   + 生命周期管理
   + 应用程序配置
   + 依赖注入容器
   + 日志记录和诊断
   
   CreateDefaultBuilder 创建基本配置

   ConfigureWebHostDefaults 对 web 主机的基本配置进行 自定义配置


ps:本文知识点解读存在一定局限性


