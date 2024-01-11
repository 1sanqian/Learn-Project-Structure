# .NET
.NET 平台是一个开发框架，支持多种编程语言，用于构建各种类型的应用程序，包括桌面应用、Web 应用、移动应用、云服务等。这个平台由 Microsoft 提供，并以其强大的工具和类库著称

**云应用**
*   [云原生应用](https://learn.microsoft.com/zh-cn/dotnet/architecture/cloud-native/)
*   [.NET Aspire](https://learn.microsoft.com/zh-cn/dotnet/aspire)
*   [控制台应用](https://learn.microsoft.com/zh-cn/dotnet/core/tutorials/with-visual-studio-code)
*   [云中的无服务器函数](https://learn.microsoft.com/zh-cn/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-csharp)
*   [Web 应用、Web API 和微服务](https://learn.microsoft.com/zh-cn/aspnet/core/introduction-to-aspnet-core#recommended-learning-path)

**跨平台客户端应用**
*   [桌面应用](https://dotnet.microsoft.com/apps/desktop)
*   [游戏](https://dotnet.microsoft.com/apps/games)
*   [移动应用](https://dotnet.microsoft.com/apps/maui)

**Windows 应用**
*   [Windows 桌面应用](https://dotnet.microsoft.com/apps/desktop)
    *   [Windows 窗体](https://learn.microsoft.com/zh-cn/dotnet/desktop/winforms/)
    *   [Windows WPF](https://learn.microsoft.com/zh-cn/dotnet/desktop/wpf/)
    *   [通用 Windows 平台 (UWP)](https://learn.microsoft.com/zh-cn/windows/uwp/get-started/create-a-hello-world-app-xaml-universal)
*   [Windows 服务](https://learn.microsoft.com/zh-cn/aspnet/core/host-and-deploy/windows-service)

**其他应用类型**
*   [机器学习](https://learn.microsoft.com/zh-cn/dotnet/machine-learning/)
*   [物联网 (IoT)](https://learn.microsoft.com/zh-cn/dotnet/iot/)

## 功能
借助 .NET 功能，开发人员可以高效地编写可靠的高性能代码

*   [异步代码](https://learn.microsoft.com/zh-cn/dotnet/csharp/asynchronous-programming/)
*   [属性](https://learn.microsoft.com/zh-cn/dotnet/standard/attributes/)
*   [反射](https://learn.microsoft.com/zh-cn/dotnet/csharp/advanced-topics/reflection-and-attributes/)
*   [代码分析器](https://learn.microsoft.com/zh-cn/dotnet/fundamentals/code-analysis/overview)
*   [委托和 lambda](https://learn.microsoft.com/zh-cn/dotnet/standard/delegates-lambdas)
*   [事件](https://learn.microsoft.com/zh-cn/dotnet/standard/events/)
*   [异常](https://learn.microsoft.com/zh-cn/dotnet/standard/exceptions/)
*   [垃圾回收](https://learn.microsoft.com/zh-cn/dotnet/standard/automatic-memory-management)
*   [泛型类型](https://learn.microsoft.com/zh-cn/dotnet/standard/generics)
*   [LINQ（语言集成查询）](https://learn.microsoft.com/zh-cn/dotnet/standard/linq/)。
*   [并行编程](https://learn.microsoft.com/zh-cn/dotnet/standard/parallel-programming/)和[托管线程](https://learn.microsoft.com/zh-cn/dotnet/standard/threading/managed-threading-basics)
*   类型推理 - [C#](https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/types/#specifying-types-in-variable-declarations)、[F#](https://learn.microsoft.com/zh-cn/dotnet/fsharp/language-reference/type-inference)、[Visual Basic](https://learn.microsoft.com/zh-cn/dotnet/visual-basic/programming-guide/language-features/variables/local-type-inference)。
*   [类型系统](https://learn.microsoft.com/zh-cn/dotnet/standard/base-types/common-type-system)
*   [不安全代码](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/unsafe-code)

## 运行时

[公共语言运行时 (CLR)](https://learn.microsoft.com/zh-cn/dotnet/standard/clr) 是生成所有 .NET 应用的基础。 [运行时的基本功能](https://github.com/dotnet/runtime/blob/main/docs/design/coreclr/botr/intro-to-clr.md)包括：

*   垃圾回收
*   内存安全和类型安全
*   对编程语言的全面支持
*   跨平台设计

.NET 有时被称为“托管代码”运行时。 之所以称为“托管”，主要是因为它使用垃圾回收器进行内存管理，还因为它强制执行类型和内存安全。 CLR 虚拟化（或抽象）了各种操作系统和硬件概念，例如内存、线程和异常

CLR 从一开始就被设计为一个跨平台运行时。 它已被移植到多个操作系统和体系结构。 通常，跨平台 .NET 代码不需要经过重新编译就能在新环境中运行。 相反，你只需安装一个不同的运行时即可运行应用

运行时为调试器、[转储](https://learn.microsoft.com/zh-cn/dotnet/core/diagnostics/dumps)和[跟踪](https://learn.microsoft.com/zh-cn/dotnet/core/diagnostics/logging-tracing)工具以及[可观测性](https://learn.microsoft.com/zh-cn/dotnet/core/diagnostics/#instrumentation-for-observability)公开各种[诊断](https://learn.microsoft.com/zh-cn/dotnet/core/diagnostics/)服务和 API。 可观测性实现主要是[围绕 OpenTelemetry 构建](https://devblogs.microsoft.com/dotnet/opentelemetry-net-reaches-v1-0/)的，可实现[灵活的应用程序监视](https://devblogs.microsoft.com/dotnet/announcing-dotnet-monitor-in-net-6/)和站点可靠性工程 (SRE)。

运行时通过结合使用 [P/Invoke](https://learn.microsoft.com/zh-cn/dotnet/standard/native-interop/)、值类型以及跨本机/托管代码边界对值进行 [blit](https://learn.microsoft.com/zh-cn/dotnet/framework/interop/blittable-and-non-blittable-types) 的功能，提供低级别的 C 样式互操作功能。

## 运行时库

.NET 具有一组全面的标准类库。 这些库为许多常规用途类型和特定于工作负载的类型和实用工具功能提供实现。

下面是在 .NET 运行时库中定义的一些类型示例：

*   每个 .NET 类型都派生自 [System.Object](https://learn.microsoft.com/zh-cn/dotnet/api/system.object) 类型
*   基元值类型，如 [System.Boolean](https://learn.microsoft.com/zh-cn/dotnet/api/system.boolean) 和 [System.Int32](https://learn.microsoft.com/zh-cn/dotnet/api/system.int32)
*   集合，例如 [System.Collections.Generic.List<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.list-1) 和 [System.Collections.Generic.Dictionary<TKey,TValue>](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.dictionary-2)
*   数据类型，例如 [System.Data.DataSet](https://learn.microsoft.com/zh-cn/dotnet/api/system.data.dataset) 和 [System.Data.DataTable](https://learn.microsoft.com/zh-cn/dotnet/api/system.data.datatable)
*   网络实用程序类型，如 [System.Net.Http.HttpClient](https://learn.microsoft.com/zh-cn/dotnet/api/system.net.http.httpclient)
*   [文件和流 I/O](https://learn.microsoft.com/zh-cn/dotnet/standard/io/) 实用程序类型，如 [System.IO.FileStream](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.filestream) 和 [System.IO.TextWriter](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.textwriter)
*   [序列化](https://learn.microsoft.com/zh-cn/dotnet/standard/serialization/)实用程序类型，例如 [System.Text.Json.JsonSerializer](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.json.jsonserializer) 和 [System.Xml.Serialization.XmlSerializer](https://learn.microsoft.com/zh-cn/dotnet/api/system.xml.serialization.xmlserializer)
*   高性能类型，例如 [System.Span<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.span-1)、[System.Numerics.Vector](https://learn.microsoft.com/zh-cn/dotnet/api/system.numerics.vector) 和 [System.Span<T>](https://learn.microsoft.com/zh-cn/dotnet/api/system.span-1)

有关详细信息，请参阅[运行时库概述](https://learn.microsoft.com/zh-cn/dotnet/standard/runtime-libraries-overview)

## .NET 历史信息

+ 2002 年，Microsoft 发布了 [.NET Framework](https://learn.microsoft.com/zh-cn/dotnet/framework/get-started/overview)，这是用于创建 Windows 应用的开发平台
+ 2014 年，Microsoft 推出了 .NET Core 作为 .NET Framework 的跨平台开源后续产品，可在 Windows、Linux 和 macOS 上运行
+ 2020年，.NET 5是.NET Core 3.1 之后的下一个版本去掉了名称中的“Core”部分，.NET 5标志着.NET Core和.NET Framework的合并。之前，这两者是分开开发和维护的，.NET 5将它们整合为一个平台，为跨平台和一致性提供更好的支持
+ 2021年， .NET 6继续加强.NET 5的合并版本，并提供更多的功能和改进，包括对Windows、Linux、macOS等平台的支持，性能改进，新的C#语言特性等，.NET 6 是从 .NET 5 开始的 .NET 统一计划的最后部分，跨平台、桌面、IoT 和云应用的统一的SDK、基本库和运行时（Runtime） 都在 .NET 6 统一
+ 2022年，.NET 7 建立在 .NET 6 建立的基础之上，.NET 7 主要关注领域包括：
    + 改进对 云原生 方案的支持
    + 升级旧项目的工具
    + 简化开发人员使用容器的难度
+ 2023年，.NET8 又带来了很多方面的增强，比如：Blazor 开发框架、人工智能、云原生.NET Aspire、性能、native AOT  等
+ .NET 5、.NET 6、.NET 7 和 .NET 8 是具有一组统一功能和 API 的单一产品，可用于 Windows 桌面应用和跨平台控制台应用、云服务和网站

基于以上版本，归类目前.net 的主要版本有： 
+ [.NET Framework](https://learn.microsoft.com/zh-cn/dotnet/framework/get-started/)
+ [.NET Core](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-core-3-1)
+ [.NET 5](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5)
+ [.NET 6](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6) 
+ [.NET 7](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-7)
+ [.NET 8](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8)

## .NET Framework

[.NET Framework](https://learn.microsoft.com/zh-cn/dotnet/framework/get-started/) 是管理面向 .NET Framework 的应用的运行时执行环境。 它包括公共语言运行时（提供内存管理和其他系统服务）和一个全面的类库（使程序员能利用强大可靠的代码实现所有主要领域的应用开发）

### 什么是 .NET Framework？ 

NET Framework 是 Windows 的托管执行环境，可为其运行的应用提供各种服务

#### 主要组件

.NET Framework包括两个主要组件：
+ 公共语言运行时 (CLR)，它是处理运行应用的执行引擎
    + 执行托管代码：其中包括将源代码编译为中间语言（IL，Intermediate Language），并在运行时将 IL 代码转换为本机代码
    + 垃圾回收：用于管理应用程序的内存。它负责分配和释放内存，防止内存泄漏和悬空引用
    +  内存安全和类型安全：通过代码访问权限和代码验证来确保.NET 应用程序的安全性。它实施了代码访问安全策略，以确保应用程序不会执行危险的操作
    +  异常处理机制：能处理.NET 应用程序中的异常，提供了一套强大的异常处理机制，允许开发者编写并捕获异常，并确保程序在异常情况下能够高效地处理
    + 对编程语言的全面支持：允许在同一应用程序中使用多种语言编写的组件
+ .NET Framework 类库，它提供开发人员可从其自己的应用中调用的已测试、可重用代码库
    + Base Class Library，BCL：包含了许多基本的类和类型，用于处理字符串、集合、IO操作、异常处理等
    + WPF：用于构建富客户端应用程序的UI框架，支持XAML语法，使开发者能够创建现代化、可视化的用户界面
    + WCF：用于构建分布式服务的框架，支持创建服务导向的应用程序，并提供通信、安全、事务等功能
    + Windows Forms：用于创建传统的桌面应用程序的UI框架，通过可视化设计工具和事件驱动模型，使界面开发更加简便
    + ADO.NET：用于访问和操作数据库的框架，提供了一系列的类和方法，支持连接到各种数据库系统
    + ASP.NET：用于构建Web应用程序的框架，支持创建动态网页、处理HTTP请求和响应、管理用户会话等
    + LINQ：提供一种统一的查询语法，使开发者能够在C#或VB.NET代码中方便地进行数据查询和操作

以下是 CLR 和类库与应用之间以及与整个系统之间的关系

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d0dc368a6e9297e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 服务

.NET Framework 提供的用于运行应用的服务包括：
+ 内存管理：CLR 代表应用负责分配和释放内存并处理对象生存期
+ 常规类型系统： 基本类型由 .NET Framework 类型系统定义，且是面向 .NET Framework 的所有语言所共有的
+ 一个全面的类库：处理常见的低级编程操作时，可通过 .NET Framework 类库使用类型及其成员的易访问库
+ 开发框架和技术：NET Framework 包括用于特定区域应用开发的库
    + 用于 Web 应用的 ASP.NET
    + 用于数据访问的 ADO.NET
    + 用于面向服务的应用的 Windows Communication Foundation（WCF）
    + 用于 Windows 桌面应用的 Windows Presentation Foundation（WPF）
+ 语言互操作性
    + 面向 .NET Framework 的语言编译器发出名为公共中间语言 (CIL) 的中间代码
+ 版本兼容性
+ 并行执行
+ 多定向


## .NET Core

 [.NET Core](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-core-3-1) 和 .NET Framework 是 Microsoft 的两个不同的软件框架，主要用于开发和运行跨平台的应用程序

### 内容

+ .NET Core 支持跨平台运行，支持在Windows、Linux 和 macOS 平台运行
+ .NET Core 不支持传统的 Windows Forms 和 WPF 桌面应用程序，但支持面向现代云应用和微服务的框架
+ .NET Core 相对于.NET Framework  具有更先进的包管理系统，支持依赖注入，对于依赖关系能更为灵活的处理
+ 与 .NET Framework 使用的 运行时 CLR 不同，.NET Core 采用全新的跨平台运行时 Core CLR
+ .NET Core 适用于 云、微服务、Web API、跨平台应用程序
+ .NET Core 引入了 Blazor WebAssembly 项目，允许在浏览器中运行 .NET 应用程序
+ .NET Core 支持 C# 8.0

**注意：.NET Core 目前已经合并进 .NET 5 中，并由后续的 .NET 5  及更高的版本进行推进**

.NET Core 3.1: https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-core-3-1

## .NET 5

 [.NET 5](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5) 是继 .NET Core 3.1 之后 .NET Core 的下一个主要版本，与 NET Core 或 .NET Framework 相比，.NET 5 会支持类型更多的应用和平台

### 内容

+ .NET 5 合并了 .NET Core 和 .NET Framework 的特性，统一为一个单一的 .NET 平台
+ .NET 5 继续支持跨平台，并增加了对 Windows、Linux 和 macOS 的更广泛的支持
+ NET 5 引入了对 Windows Forms 和 WPF 的支持，使得开发者能够在 .NET 5 中使用这些传统框架
+ .NET 5 进一步加强了 Blazor WebAssembly，并提供更多的 WebAssembly 支持
+ .NET 5 引入了新的 .NET SDK，整合了 .NET Core 和 .NET Framework 的开发工具
+ .NET 5 提供对 .NET Standard 类库的支持，可以更方便地在各种 .NET 平台中重用类库
+ .NET 5 支持多种应用类型，包括 ASP.NET Core、Blazor、控制台应用、Windows 窗体、WPF 等
+ .NET 5 引入了 C# 9.0，并提供了一些新的语言特性
+ .NET 5引入了一些性能优化和新的 JIT 编译器
+ .NET 5 不会替换 .NET Framework，也不会替代 .NET Standard

### 改进和新功能
+   [C# 更新](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5#c-updates)
+   [F# 更新](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5#f-updates)
+   [Visual Basic 更新](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5#visual-basic-updates)
+   [System.Text.Json 新功能](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5#systemtextjson-new-features)
+   [单一文件应用](https://learn.microsoft.com/zh-cn/dotnet/core/deploying/single-file/overview)
+   [应用剪裁](https://devblogs.microsoft.com/dotnet/app-trimming-in-net-5)
+   Windows Arm64 和 Arm64 内部函数
+   转储调试的工具支持
+   对于[可以为 null 的引用类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/nullable-references)，运行时库的批注百分比为 80%
+   性能改进：
    +   [垃圾回收 (GC)](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-5/#gc)
    +   [System.Text.Json](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-5/#json)
    +   [System.Text.RegularExpressions](https://devblogs.microsoft.com/dotnet/regex-performance-improvements-in-net-5)
    +   [异步 ValueTask 池](https://devblogs.microsoft.com/dotnet/async-valuetask-pooling-in-net-5)
    +   [容器大小优化](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)
    +   [更多区域](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-5)

**注意：**
+ .NET 5 版本跳过编号 4.x，以避免与 .NET Framework 4.x 混淆，并从名称中删除了“Core”，强调 .NET 未来的主要实现
+ SP.NET Core 5.0 虽以 .NET 5 为基础，但保留了名称“Core”以避免与 ASP.NET MVC 5 混淆
+ Entity Framework Core 5.0 保留了名称“Core”，以避免与 Entity Framework 5 和 Entity Framework 6 混淆

.NET 5: https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-5

## .NET 6

[.NET 6](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6) 提供 .NET 统一计划的最终部分，该计划在 .NET 5 中启动。 .NET 6 在移动、桌面、IoT 和云应用之间统一了 SDK、基础库和运行时

### 内容

+ .NET 6 继续提升跨平台性能，并支持 Windows、Linux、macOS、iOS、Android 和浏览器 WebAssembly
+  进一步改进了 ASP.NET Core，并提供了新的 Web 开发功能，包括 Blazor 的增强支持
+ .NET 6 继续增强 Windows Forms 和 WPF 的支持，提供更多功能
+ .NET 6 引入了新的 .NET SDK，整合了 .NET Core 和 .NET Framework 的开发工具
+ .NET 6 提供了更多的类库和框架，以支持更广泛的应用类型，包括移动应用、嵌入式设备、云原生应用等
+ .NET 6 支持 Windows、Azure 和云原生应用的更好集成，包括对 Windows ARM64 的支持
+ .NET 6 进一步支持多种应用类型，包括 ASP.NET Core、Blazor、控制台应用、Windows 窗体、WPF 等
+ .NET 6 引入了 C# 10.0，提供了新的语言特性
+ .NET 6 继续优化性能，提供更好的运行时性能和资源利用率

### 改进和新功能
 
+ [JIT 性能改进](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-6/#jit)
+ [Crossgen2](https://devblogs.microsoft.com/dotnet/conversation-about-crossgen2/)
+ [Arm64 支持](https://github.com/dotnet/sdk/issues/22380)
+ [热重载](https://devblogs.microsoft.com/dotnet/update-on-net-hot-reload-progress-and-visual-studio-2022-highlights/)
+ [.NET MAUI](https://devblogs.microsoft.com/dotnet/update-on-dotnet-maui/)
+ [C# 10](https://learn.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-10) 和 [模板](https://devblogs.microsoft.com/dotnet/announcing-net-6-release-candidate-2/#net-sdk-c-project-templates-modernized)
+ [F#](https://learn.microsoft.com/zh-cn/dotnet/fsharp/whats-new/fsharp-6)
+ SDK 工作负载
+ [System.Text.Json](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.json) APIs
+ [源生成器](https://learn.microsoft.com/zh-cn/dotnet/csharp/roslyn-sdk/source-generators-overview)
+ [可写 DOM](https://learn.microsoft.com/zh-cn/dotnet/standard/serialization/system-text-json/use-dom?pivots=dotnet-6-0#json-dom-choices)
    +   [JsonNode](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.json.nodes.jsonnode)
    +   [JsonArray](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.json.nodes.jsonarray)
    +   [JsonObject](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.json.nodes.jsonobject)
    +   [JsonValue](https://learn.microsoft.com/zh-cn/dotnet/api/system.text.json.nodes.jsonvalue)
+ [IAsyncEnumerable 序列化](https://learn.microsoft.com/zh-cn/dotnet/core/compatibility/serialization/6.0/iasyncenumerable-serialization)
+ [其他新 API](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6#other-new-apis)
+ [HTTP/3](https://learn.microsoft.com/zh-cn/dotnet/core/extensions/httpclient-http3)
+ [ASP.NET Core](https://learn.microsoft.com/zh-cn/aspnet/core/release-notes/aspnetcore-6.0)
+ [OpenTelemetry](https://opentelemetry.io/)
+ [.NET 包验证](https://learn.microsoft.com/zh-cn/dotnet/fundamentals/apicompat/package-validation/overview)
+ [性能改进：](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-6/)
    + [FileStream](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6#filestream)
    + [按配置优化](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6#profile-guided-optimization)
    + [AOT 编译](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6#crossgen2)

.NET 6: https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-6

## .NET 7

[.NET 7](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-7) 是 .NET 6 的后继版本，侧重于统一、新式、简单和快速的特点

### 内容

+ .NET 7 的多平台应用程序 UI (MAUI) 将 Android、iOS、macOS 和 Windows API 统一到一个API中，以提供了一个项目来处理跨设备及其平台的多目标
+ .NET 7 进一步改进了 ASP.NET Core
+ .NET 7 继续增强 Windows 窗体 和 WPF 的支持，提供更多功能
+  .NET 库 API 做出了许多改进
+ .NET 7 对反射、堆栈替换 (OSR)、启动时间、本机 AOT、循环优化和许多其他领域进行优化改进
+ .NET 7 改进了 Arm64 的代码生成 和 正则表达式
+ .NET 7 [SDK](https://learn.microsoft.com/zh-cn/dotnet/core/sdk) 提升了 CLI 模板体验
+ .NET 7 改进了可观测性，帮助了解在应用缩放时以及技术复杂性增加时应用的状态
+ .NET 7 引入了 C# 11，提供了新的语言特性

### 改进和新功能

+ [C# 11](https://learn.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-11)
+ [F# 7](https://devblogs.microsoft.com/dotnet/announcing-fsharp-7/)
+ [.NET MAUI](https://learn.microsoft.com/zh-cn/dotnet/maui/whats-new/dotnet-7)
+ [ASP.NET Core](https://learn.microsoft.com/zh-cn/aspnet/core/release-notes/aspnetcore-7.0)
+ [EF Core](https://learn.microsoft.com/zh-cn/ef/core/what-is-new/ef-core-7.0/whatsnew)
+ [Windows 窗体](https://devblogs.microsoft.com/dotnet/winforms-enhancements-in-dotnet-7)
+ [WPF](https://devblogs.microsoft.com/dotnet/wpf-on-dotnet-7/)
[.NET 升级助手和 CoreWCF](https://learn.microsoft.com/zh-cn/dotnet/core/porting/upgrade-assistant-wcf)
+ [System.Text.Json 序列化](https://devblogs.microsoft.com/dotnet/system-text-json-in-dotnet-7/)
+ [正则表达式](https://learn.microsoft.com/zh-cn/dotnet/standard/base-types/regular-expression-source-generators)
+ [.NET 类库](https://devblogs.microsoft.com/dotnet/use-net-7-from-any-javascript-app-in-net-7/)
+ [可观察性](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-4/#observability) - [OpenTelemetry](https://opentelemetry.io/)
+ [ML.NET](https://devblogs.microsoft.com/dotnet/introducing-the-ml-dotnet-text-classification-api-preview/)
+ [性能改进](https://devblogs.microsoft.com/dotnet/performance_improvements_in_net_7/)

.NET 7: https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-7

## .NET 8

[.NET 8](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8) 是 .NET 7 的后继版本，着重强调云、性能、全栈 Blazor、AI 和 Native AOT 是.NET 8的主要亮点

### 内容

+ .NET 8 提供云就绪堆栈.NET Aspire，.NET Aspire是一个用于使用 .NET 构建弹性、可观察和可配置的云原生应用程序的堆栈
+ .NET 8 可以无需等待 JIT（即时）编译器在运行时编译代码，无需部署JIT编译器和IL代码。AOT 应用程序仅部署应用程序所需的代码，应用程序现在可以在不允许使用 JIT 编译器的受限环境中运行
+ .NET 8 中的 Blazor 可以同时使用服务器和客户端来处理所有的 Web UI 需求，通过专注于优化页面加载时间、可扩展性和提升用户体验的多项新增强功能，可以在同一应用程序中使用Blazor Server 和 Blazor WebAssembly，在运行时自动将用户从服务器转移到客户端
+ .NET 8 可以通过 .NET SDK 中的AI 功能以及与多种工具的无缝集成来轻松利用 AI
+ .NET MAUI 提供单一项目系统和单一代码库来构建 WinUI、Mac Catalyst、iOS 和 Android 应用程序
+ [使用 .NET 比以往更轻松、更安全地使用容器](https://devblogs.microsoft.com/dotnet/securing-containers-with-rootless/)打包应用程序。每个 .NET 映像都包含一个非 root 用户，从而通过单行配置启用更安全的容器


### 改进和新功能

+ [.NET Aspire](https://learn.microsoft.com/zh-cn/dotnet/aspire)
+ [ASP.NET Core](https://learn.microsoft.com/zh-cn/aspnet/core/release-notes/aspnetcore-8.0)
+ Core .NET 库：
    +   [序列化](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#serialization)
    +   [时间抽象](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#time-abstraction)
    +   [UTF8 改进](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#utf8-improvements)
    +   [处理随机性的方法](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#methods-for-working-with-randomness)
    +   [以性能为中心的类型](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#performance-focused-types)
    +   [System.Numerics 和 System.Runtime.Intrinsics](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#systemnumerics-and-systemruntimeintrinsics)
    +   [数据验证](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#data-validation)
    +   [度量值](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#metrics)
    +   [加密](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#cryptography)
    +   [网络连接](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#networking)
    +   [基于流的 ZipFile 方法](https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8?source=recommendations#stream-based-zipfile-methods)
+ [.NET MAUI](https://learn.microsoft.com/zh-cn/dotnet/maui/whats-new/dotnet-8)

备注： 由于.NET 8 由2023年11月24 发布，作为长期支持版本，目前仍处于维护阶段，故
+ 新框架可能存在一些未知的问题和漏洞，这会影响到开发的稳定性和可靠性
+ 新框架可能与现有的代码库和工具不兼容，这会导致一些问题和限制
+ 新框架可能还没有完善的文档和社区支持，这会给开发人员带来很大的困扰
+ 新框架可能存在一些潜在的风险因素，如安全问题、性能问题等，这会影响到开发的效率和质量

.NET 8: https://learn.microsoft.com/zh-cn/dotnet/core/whats-new/dotnet-8

## .NET Standard

[.NET Standard](https://learn.microsoft.com/zh-cn/dotnet/standard/net-standard?tabs=net-standard-1-0) 是一种规范，定义了一组API，用于在不同的.NET平台上实现可移植的代码。它的目的是为不同的 .NET 平台提供一致的API，以便开发人员可以编写可移植的代码，而不必担心运行时环境的不同
 
### .NET Standard 版本控制规则

+ 累加性：.NET Standard 较高的版本会包含较低版本的所有 API
+ 不可变：一旦发布，.NET Standard 版本就会冻结起来

### .NET Standard 实现形式
+ .NET Standard 引用程序集的主要分发载体是 NuGet 包，实现会以适用于每个 .NET 实现的各种方式提供
+ NuGet 包面向一个或多个[框架](https://learn.microsoft.com/zh-cn/dotnet/standard/frameworks) + .NET Standard 包面向“.NET Standard”框架

### .NET Standard 问题

1. API 的限制：由于 .NET Standard 是为了提供一组共享 API 而创建的，因此它可能会限制某些特定框架或平台上可用的 API；

.NET 5+ 中的解决方案：实现某项功能时，该功能便已可供所有 .NET 5+ 应用和库使用，因为代码基底是共享的

2. 版本控制问题：由于 .NET Standard 是一个版本化的框架，API 规范与其实现的分离导致 API 规范版本与实现版本之间出现复杂的映射，因此可能会出现版本控制问题，例如需要使用不同版本的 .NET Standard 来支持不同的 API；

NET 5+ 中的解决方案： .NET 5+ API 规范与 它的 实现之间不存在任何分离

3. 兼容性问题：由于 .NET Standard 是为了解决 .NET Framework、.NET Core 和 Xamarin 之间的兼容性问题而创建的，.NET Standard 公开了特定于平台的 API，因此当它在不具有给定 API 实现的平台上运行时，可能会出现与其他框架或平台不兼容的问题；

.NET 5+ 中的解决方案：.NET 5+ SDK 包括默认启用的代码分析器。 平台兼容性分析器会检测在其上运行的平台所不支持的 API 的意外使用情况

.NET Standard: https://learn.microsoft.com/zh-cn/dotnet/standard/net-standard?tabs=net-standard-1-0#net-standard-versions

本文知识点存在一定局限性，具体疑难点请参考官网：https://learn.microsoft.com/zh-cn/dotnet/core/introduction
