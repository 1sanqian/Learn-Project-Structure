# MiddleWares

## MiddleWares 的概述

在C# 中，ASP.NET Core 应用程序的处理流程通过一个称为管道（pipeline）的概念来组织，而中间件就是在这个管道中起作用的一部分，中间件是一种装配到应用管道以处理HTTP 请求和响应的软件，每个组件：
+ 选择是否将请求传递到管道中的下一个组件
+ 可在管道中的下一个组件前后执行工作

### MiddleWares 的工作原理

中间件按照配置的顺序组成一个管道，处理传入的 HTTP 请求并生成相应的 HTTP 响应：

1. 请求流向： 当 ASP.NET Core 应用程序接收到一个 HTTP 请求时，请求会被传递到应用程序的中间件管道
2. 中间件管道： 中间件管道是由一系列中间件组成的，它们按照在 Startup 类的 Configure 方法中的配置顺序执行
3. 中间件处理：每个中间件都有一个机会处理请求，中间件通过实现 `Invoke` 方法（或 `InvokeAsync` 方法）来执行自己的逻辑，`Invoke` 方法接收一个 `HttpContext` 对象 和 一个 `Func<Task>` （表示下一个中间件或终端中间件的委托）
    + 请求委托由一系列中间件组成，每个中间件都负责对请求进行一些处理
    + 请求委托用于生成请求管道，处理每个 HTTP 请求


```
public async Task InvokeAsync(HttpContext context, Func<Task> next)
{
    // 在中间件执行前的逻辑

    await next(); // 调用下一个中间件或终端中间件

    // 在中间件执行后的逻辑
}
```

4. 传递请求： 每个中间件可以选择将请求传递给下一个中间件，也可以决定短路管道，不将请求传递给下一个中间件
5. 异步处理：中间件通常是异步执行的，可以执行 异步操作 并 等待 其他中间件 完成后 继续执行
6. 生成响应： 终端中间件或最后一个中间件负责生成 HTTP 响应，若中间件短路，请求将在到达 终端中间件 之前结束，终端中间件生成响应并 将其发送回客户端
7. HttpContext： `HttpContext`  对象是中间件工作的上下文，包含有关的请求和响应信息，以及一些有关当前执行环境的其他信息

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d9757a3d45423aca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用中间件的具体流程：
1.  异常/错误处理
    +   当应用在开发环境中运行时：
        +   开发人员异常页中间件 ([UseDeveloperExceptionPage](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)) 报告应用运行时错误
        +   数据库错误页中间件 ([UseDatabaseErrorPage](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.databaseerrorpageextensions.usedatabaseerrorpage)) 报告数据库运行时错误
    +   当应用在生产环境中运行时：
        +   异常处理程序中间件 ([UseExceptionHandler](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)) 捕获以下中间件中引发的异常
        +   HTTP 严格传输安全协议 (HSTS) 中间件 ([UseHsts](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.hstsbuilderextensions.usehsts)) 添加 `Strict-Transport-Security` 标头
2.  HTTPS 重定向中间件 ([UseHttpsRedirection](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection)) 将 HTTP 请求重定向到 HTTPS
3.  静态文件中间件 ([UseStaticFiles](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles)) 返回静态文件，并简化进一步请求处理
4.  Cookie 策略中间件 ([UseCookiePolicy](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy)) 使应用符合欧盟一般数据保护条例 (GDPR) 规定
5.  用于路由请求的路由中间件 ([UseRouting](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.userouting))
6.  身份验证中间件 ([UseAuthentication](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication)) 尝试对用户进行身份验证，然后才会允许用户访问安全资源
7.  用于授权用户访问安全资源的授权中间件 ([UseAuthorization](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.authorizationappbuilderextensions.useauthorization))
8.  会话中间件 ([UseSession](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession)) 建立和维护会话状态。 如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件
9.  用于将 Razor Pages 终结点添加到请求管道的终结点路由中间件（带有 [MapRazorPages](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.razorpagesendpointroutebuilderextensions.maprazorpages) 的 [UseEndpoints](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints)）


![image.png](https://upload-images.jianshu.io/upload_images/29476859-73bb7e90f549c207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

⚠️ 注意：
+ 每个委托均可在下一个委托前后执行操作，应尽早在管道中 调用处理委托，以便捕获在管道后期阶段发生的异常
+ 当委托不将请求传递给下一个委托时，称为“为请求管道让路”，这表示当前中间件已经处理了请求并生成了相应的响应，不需要将请求传递给管道中的后续中间件，通常需要短路，可以避免不必要的工作
    + 在向客户端发送响应后，请勿调用 `next.Invoke`。 响应启动后，针对 [HttpResponse](https://learn.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.http.httpresponse) 的更改将引发异常

## MiddleWares 的种类

在 ASP.NET Core 中，有许多内置的中间件和社区提供的中间件，可以用于处理不同方面的请求和响应。以下是一些常见的中间件类型：

###  静态文件中间件（UseStaticFiles）

静态文件中间件 是用于提供静态文件，如CSS、JavaScript和图像，而无需借助额外的服务器或处理逻辑

1. 配置静态文件中间件： 在 Startup 类的 Configure 方法中配置静态文件中间件，使用 UseStaticFiles 方法启动该 中间件

```
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();
    // 其他中间件配置...
}
```

2. 默认文件： 静态文件中间件运行指定默认的文件，当请求的路径是一个目录时，中间件将尝试返回默认文件

```
app.UseDefaultFiles();
```

3.  自定义静态文件路径：可以通过 UseStaticFiles 方法的参数指定静态文件的路径，默认情况下，中间件将在应用程序的根目录中查找静态文件

```
app.UseStaticFiles(new StaticFileOptions
{
    // 静态文件将从应用程序根目录下的 "MyStaticFiles" 文件夹提供，并且在 URL 中使用 "/static" 前缀
    FileProvider = new PhysicalFileProvider(Path.Combine(Directory.GetCurrentDirectory(), "MyStaticFiles")),
    RequestPath = "/static"
});
```

4. 缓存控制： 静态文件中间件还提供缓存控制的选项，可以配置浏览器缓存策略

```
app.UseStaticFiles(new StaticFileOptions
{
    OnPrepareResponse = context =>
    {
        // 设置缓存策略, max-age=604800 表示浏览器将缓存文件一周
        context.Context.Response.Headers.Append("Cache-Control", "public, max-age=604800");
    }
});
```

5. 启用 目录 浏览： 静态文件中间件还可以启用目录浏览，以便查看目录中的文件

```
app.UseDirectoryBrowser();
```

###  身份验证中间件 (UseAuthentication)

身份验证中间件是 ASP.NET Core 中用于 处理用户身份验证的关键组件，负责验证用户的身份，并在用户提供认证是生成相应的身份信息

1. 配置身份验证中间件： 在 `Startup` 类的 `ConfigureServices` 的方法中配置身份验证服务，使用  AddAuthentication 方法来启用身份验证

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultAuthenticateScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    })
    .AddCookie(options =>
    {
        options.Cookie.Name = "YourAuthCookieName"; // 使用 Cookie 身份验证方案，并指定配置
        // 配置其他选项...
    });

    // 其他服务配置...
}
```

2. 在中间件管道中使用身份验证： 在 Configure 方法中将 身份验证中间件添加到中间件管道中，通常放在 UseRouting 和 UseEndpoints 之间

```
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        // 配置终结点...
    });
}
```

3. 添加身份验证控制器： 在需要进行身份验证的控制器 或 动作 上 添加 [Authorize] 属性，以确保只有经过身份验证的用户才能访问

```
[Authorize]
public class SecureController : Controller
{
    // 受身份验证保护的操作...
}
```

4. 登录和注销：身份验证中间件 还提供了一些方法用于登录和注销用户

```
[HttpPost]
public async Task<IActionResult> Login(LoginViewModel model)
{
    // 在控制器中可以使用 SignInManager 来登录用户
    var result = await _signInManager.PasswordSignInAsync(model.UserName, model.Password, model.RememberMe, lockoutOnFailure: false);
    
    if (result.Succeeded)
    {
        // 登录成功的处理逻辑
        return RedirectToAction("Index", "Home");
    }
    else
    {
        // 登录失败的处理逻辑
        ModelState.AddModelError(string.Empty, "Invalid login attempt.");
        return View(model);
    }
}

// 注销用户可以使用 SignOut
[HttpPost]
public async Task<IActionResult> Logout()
{
    await _signInManager.SignOutAsync();
    return RedirectToAction("Index", "Home");
}
```

###  授权中间件 (UseAuthorization)

授权中间件 是 ASP.NET Core中用于处理 用户授权的关键组件，负责检查用户是否具体访问受保护资源的权限

1.  配置授权中间件：在 startup 类中 ConfigureServices 方法中配置授权服务，使用 AddAuthorization 方法来启用授权

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        // 使用 AddPolicy 方法添加了一个名为 "RequireAdminRole" 的授权策略，该策略要求用户具有 "Admin" 角色
        options.AddPolicy("RequireAdminRole", policy => policy.RequireRole("Admin"));
        // 添加其他授权策略...
    });

    // 其他服务配置...
}
```

2. 在中间件管道中使用授权：在 Configure 方法中将授权中间件到中间件管道中，通常放在 UseRouting 和 UseEndpoints 之间

```
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();  // 用于启用授权中间件

    app.UseEndpoints(endpoints =>
    {
        // 配置终结点...
    });
}
``` 

3. 在控制器或动作上 应用授权策略： 在需要进行授权的控制器或动作上 使用 [Authorize] 属性，并指定要应用的授权策略

```
[Authorize("RequireAdminRole")]
public class AdminController : Controller
{
    // 需要 "Admin" 角色才能访问的操作...
}
```

4. 自定义授权策略： 可自定义授权策略

```
// 自定义了一个授权策略 "RequireCustomClaim"，要求用户具有名为 "CustomClaim" 且值为 "Allowed" 的声明
services.AddAuthorization(options =>
{
    options.AddPolicy("RequireCustomClaim", policy =>
        policy.RequireAssertion(context =>
            context.User.HasClaim(c => c.Type == "CustomClaim" && c.Value == "Allowed")));
});
```

###  路由中间件 (UseRouting)

路由中间件是用于处理 HTTP 请求并将其路由到相应的处理程序（通常是控制器中的动作方法）的关键组件

1. 配置路由中间件：在 Startup 类的 Configure 方法中 配置路由中间件，通常，路由中间件会在 UseRouting 和 UseEndpoints 之间进行配置

```
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    // 配置路由中间件
    app.UseEndpoints(endpoints =>
    {
        // 配置了一个默认的控制器路由，并将请求路由到名为 default 的路由规则
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

2. 路由参数： 路由中间件支持路由参数，可以通过占位符在路由模版中指定

```
// 示例路由模板, {controller}、{action}、{id} 都是常见的路由参数
"{controller=Home}/{action=Index}/{id?}"
```

3. Attribute 路由：除了可以在 Startup 中配置路由规则外，还可以使用控制器和动作方法上的 Route 特性进行路由配置

```
[Route("custom/[controller]")]
public class MyController : Controller
{
    // Route 特性指定了自定义的控制器和动作方法的路由
    [Route("custom-action/{id}")]
    public IActionResult MyAction(int id)
    {
        // 动作方法的逻辑...
    }
}
```

4. 路由约束：路由中间件还支持路由约束，以限制参数的匹配规则

```
// 数字约束
"{id:int}"

// 字符串约束
"{name:minlength(3)}"
```

###  端点中间件 (UseEndpoints)

端点中间件通常指的是在请求处理管道的最后阶段，通过 UseEndpoints 方法配置的中间件，这个中间件负责处理传入的 HTTP 请求并路由到相应的控制器和动作方法

1. 配置端点中间件：在 Startup 类 的 Configure 方法中，使用 UseEndpoints 方法配置 端点中间件，通常是在路由中间件之后配置的


```
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        // 配置控制器和动作方法的路由
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // 配置端点中间件 ：配置了一个回退路由，将请求路由 到 Home 控制器的 Index 的动作方法
        endpoints.MapFallbackToController("Index", "Home");
    });
}
```

2. 路由参数和约束：端点中间件支持与 路由相关的参数和约束，可以在 MapControllerRoute 方法中配置

```
// 定义了一个名为 custom 路由，指定了控制器、动作方法和一个可选的整数类型的参数
endpoints.MapControllerRoute(
    name: "custom",
    pattern: "{controller=Custom}/{action=Index}/{id:int?}");
```

3. MapFallback  方法： 除了 MapFallbackToController ，还可以使用 MapFallBack 方法配置自定义的回退处理 逻辑

```
endpoints.MapFallback(context => 
{
    // 自定义的回退处理逻辑
    context.Response.WriteAsync("Fallback response");
});
```

###  错误处理中间件

错误处理中间件 是 用于捕获应用程序中发生的异常并提供用户友好错误的页面或日志记录的中间件

1. 配置错误处理 中间件：在 Startup 类的 Configure  方法中配置 错误 处理 中间件，通常 错误 处理中间件 会在其他中间件之后配置

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage(); // 在开发环境中使用开发者异常页面
    }
    else
    {
        app.UseExceptionHandler("/Home/Error"); // 在生产环境中使用自定义错误处理页面
        app.UseStatusCodePagesWithReExecute("/Home/Error/{0}"); // 在生产环境中使用状态码页面
    }

    // 其他中间件配置...
}
```

2. 自定义错误页面： 可以为不同的  HTTP 状态码 配置  自定义 的错误页面

```
// 使用 UseStatusCodePagesWithReExecute 来为不同的状态码配置自定义的错误页面
app.UseStatusCodePagesWithReExecute("/Home/CustomError/{0}");
```

3. 开发者异常页面： 在开发环境中，可以使用 UseDeveloperExceptionPage  来显示详细的开发者异常页面，包含堆栈跟踪等信息

```
app.UseDeveloperExceptionPage();
```

### Session 中间件 (UseSession)

Session 中间件用于 在应用程序中启用和管理 会话状态，会话状态允许在用户的多个请求之间存储和检索数据

1. 配置 Session 中间件： 在 Startup 类的 ConfigureServices  方法中 配置 Session 服务 ，并在
Configure 方法中启用 Session 中间件

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddSession(options =>
    {
        // 配置Session选项
        options.IdleTimeout = TimeSpan.FromMinutes(20);
    });

    // 其他服务配置...
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSession(); // 启用Session中间件

    // 其他中间件配置...
}
``` 

2. 在控制器中 使用 Session：可以在控制器 或视图 中 使用 HttpContext.Session 属性来存储和检索会话数据

```
// 写入Session数据
HttpContext.Session.SetString("UserName", "John");

// 读取Session数据
var userName = HttpContext.Session.GetString("UserName");
```

3. 配置 Session 选项： 通过 AddSession 方法 的参数配置 Session 选项

```
services.AddSession(options =>
{
    // 设置了会话的空闲超时时间为20分钟
    options.IdleTimeout = TimeSpan.FromMinutes(20);
});
```

4. 注意事项
+ Session 中间件依赖于底层的数据存储，在开发环境中，可以使用内存存储；在生产环境中，建议使用分布式缓存或数据库等可扩展的存储方式
+ 要使用 Session 中间件，需要在应用程序中 启用 Cookies 中间件，因为 Session 数据通常存储在客户端的 Cookie 中

### 跨域资源共享 (CORS) 中间件 (UseCors)

跨域资源共享CORS 中间件 是用于处理 跨域请求的组件，允许在不同域 的客户端发起对应用程序 的请求，通过配置服务器响应头 来授权跨域请求，通过配置 CORS 中间件，可以控制应用程序对跨域请求的响应，提供安全的跨域资源的共享机制，防止恶意网站利用资源

1. 配置 CORS 中间件： 在 Startup 类的 ConfigureServices 方法中 配置 CORS 服务，并在 Configure 方法中启用 CORS 中间件

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });

    // 其他服务配置...
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseCors("AllowSpecificOrigin"); // 启用CORS中间件

    // 其他中间件配置...
}
```

2. CORS 策略配置： 可以配置多个 CORS 策略，并为每个策略指定允许的来源、允许的方法、允许的标头等

```
services.AddCors(options =>
{
    options.AddPolicy("AllowSpecificOrigin",
        builder => builder
            .WithOrigins("https://example.com")
            .AllowAnyMethod()
            .AllowAnyHeader());
    
    options.AddPolicy("AllowAllOrigins",
        builder => builder
            .AllowAnyOrigin()
            .AllowAnyMethod()
            .AllowAnyHeader());
});
```

3. 全局CORS 配置： 可以使用全局配置允许所有 请求跨域，在生产环境中要慎重使用

```
services.AddCors(options =>
{
    options.AddPolicy("AllowAll",
        builder => builder
            .AllowAnyOrigin()
            .AllowAnyMethod()
            .AllowAnyHeader());
});
```

4. 在控制器 中 使用 CORS: 可以在控制器或动作方法上使用 [EnableCors] 特性来覆盖全局CORS 配置

```
[EnableCors("AllowSpecificOrigin")]
public class MyController : Controller
{
    // 控制器的逻辑...
}
```

### MVC 中间件 (UseMvc)

MVC （Model-View-Controller） 中间件 是用于构建 Web 应用程序的核心组件。MVC 模式 将应用程序分为 模型 Model、视图 View 和 控制器 Controller 三个部分，通过中间件实现了 请求的路由、处理、和响应

1. 配置 MVC 中间件： 在 Startup 类的 ConfigureServices 方法中 配置 MVC 服务，并在 Configure 方法中 启用 MVC 中间件

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(); // 配置MVC服务

    // 其他服务配置...
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseMvc(); // 启用MVC中间件

    // 其他中间件配置...
}
```

2. MVC 路由： MVC 中间件通过路由来映射 HTTP 请求到相应的控制器和动作方法

```
// Route 特性定义了控制器和动作方法的路由
[Route("api/[controller]")]
public class MyController : Controller
{
    [HttpGet]
    public IActionResult Get()
    {
        // 处理GET请求的逻辑...
    }
}
```

3. 控制器和动作方法：控制器是处理 HTTP 请求的主要组件，动作方法是控制器中实际执行的逻辑

```
public class MyController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

4. 视图：视图用于渲染用户界面，通常与控制器中的动作方法相关联

```
public IActionResult Index()
{
    return View();
}
```

### 日志记录中间件

日志记录中间件并不是 一个单独的中间件，而是通过内置的日志记录提供程序来实现的，ASP.NET Core 内置了丰富的日志记录功能，可以通过配置使用不同的提供程序，如Console、Debug、EventSource、File、Seq 等，实现应用程序中 的 日志记录，监视应用程序的行为和性能

1. 配置日志记录服务：在 Startup 类的 ConfigureServices 方法中 配置日志记录服务

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddLogging(); // 配置日志记录服务

    // 其他服务配置...
}
```

2.  使用日志的记录：在控制器、中间件、或其他服务中使用依赖注入来获取 ILogger<T> 实例，T 是 包含日志记录的类名

```
public class MyService
{
    private readonly ILogger<MyService> _logger;

    // 通过 构造函数注入 ILogger<MyService> 实例
    public MyService(ILogger<MyService> logger)
    {
        _logger = logger;
    }

    public void DoSomething()
    {
        // 然后使用 LogInformation 方法记录信息日志
        _logger.LogInformation("Doing something...");
    }
}
```

3. 配置日志级别： 可以通过配置来设置日志记录的最低级别，以便过滤低于该级别的日志

```
public void ConfigureLogging(ILoggingBuilder logging)
{
    logging.SetMinimumLevel(LogLevel.Information); // 通过 SetMinimumLevel 方法设置了日志记录的最低级别为 Information
    logging.AddConsole(); // 使用Console日志记录提供程序
}
```

4. 使用其他提供程序： 除了 Console，还可以使用其他日志记录提供程序，如 File、EventSource、Seq 等

```
public void ConfigureLogging(ILoggingBuilder logging)
{
    logging.AddFile("logs/mylog.txt"); // 使用File日志记录提供程序
    logging.AddEventSourceLogger(); // 使用EventSource日志记录提供程序
    logging.AddSeq("http://seq-server"); // 使用Seq日志记录提供程序
}
```



## 配置 中间件 

### Map

Map 用于根据 请求的路径或其他条件将 请求 路由到 指定的分支中 

```
// 如果请求路径为"/path"，则会执行 Run 方法中 的逻辑
app.Map("/path", branch => 
{
    branch.Run(async context =>
    {
        await context.Response.WriteAsync("Mapped Path");
    });
});
```

### Run

Run 用于指定在请求处理管道中的某一点执行的逻辑，通常用于终端中间件，终端中间件只会在请求流水线中执行一次，并且不会将请求传递给下一个中间件，即不再调用管道中的下一个中间件

```
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from the terminal middleware!");
});
```

### Use

Use 用于添加中间件到请求处理管道中，可以用于执行一些逻辑，并将请求传递给下一个中间件

```
app.Use(async (context, next) =>
{
    // 在中间件之前执行一些操作
    await context.Response.WriteAsync("Before next middleware");

    // 将请求传递给下一个中间件
    await next.Invoke();

    // 在中间件之后执行一些操作
    await context.Response.WriteAsync("After next middleware");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("End of pipeline");
});
```

+ Map 用于分支路由
+ Run 用于终端中间件
+ Use 用于添加一般的中间件

中间件： https://learn.microsoft.com/zh-cn/aspnet/core/fundamentals/middleware/?view=aspnetcore-8.0
