# 一、 async

同步和异步是两种不同的执行模式，在执行方法和线程利用上会有所不同

同步是按照程序流由上到下的方式顺序执行程序，必须等待程序上一步的操作执行完，才可以执行下一步程序

异步是在遇到特定的异步修饰符时，不会等待方法完成，而是直接进入下一个方法或程序中，可在异步操作执行时继续执行，执行方法是无序的，且执行的速度也比较快。
1. 异步修饰符[Async](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/)和等待修饰符[Await](https://learn.microsoft.com/en-us/dotnet/visual-basic/language-reference/error-messages/bc42358)

异步方法通常都用关键字async修饰方法，而由关键字修饰的方法Async至少包含一个Await表达式或语句,await就是等待修饰符

⚠️注意：
 1. 异步方法旨在成为非阻止操作。
    （1）阻止操作 vs. 非阻止操作
    阻止操作（Blocking Operation）：阻止操作会使得调用该操作的线程暂停执行，直到该操作完成。例如，当一个程序执行一个阻止性I/O操作（如读取文件或等待网络响应）时，程序会停下来等待，直到操作完成才会继续执行后续代码。这种方式的缺点是如果操作耗时较长，会导致程序响应变慢，甚至无响应。
    （2）非阻止操作（Non-Blocking Operation）：非阻止操作不会暂停线程的执行。相反，这些操作会在后台进行处理，而程序可以继续执行其他代码。当非阻止操作完成时，会通过回调、事件或其他机制通知程序。这种方式的好处是可以提高程序的响应性和性能，特别是在需要处理大量I/O操作或需要长时间等待的任务时。
2. 异步方法中的 Await 表达式在等待的任务正在运行时不会阻止当前线程。 相反，表达式在继续时注册方法的其余部分并将控件返回到异步方法的调用方
3. Async 和 Await 关键字不会创建其他线程。 因为异步方法不会在其自身线程上运行，因此它不需要多线程。
4. 只有当方法处于活动状态时，该方法将在当前同步上下文中运行并使用线程上的时间。
5. 可以使用 Task.Run 将占用大量 CPU 的工作移到后台线程，但是后台线程不会帮助正在等待结果的进程变为可用状态。

在有 async修饰的异步方法中，该方法同步运行，直到到达第一个Await，此时它会挂起，直到等待的任务完成。同时，控制权返回给方法的调用者。如果该方法不包含Await表达式或语句，则该方法不会挂起并像同步方法一样执行。在有 async修饰的异步方法中没有使用await,编译器警告会提醒您任何不包含的异步方法，Await因为这种情况可能表明存在错误。有关详细信息，请参阅[编译器错误](https://learn.microsoft.com/en-us/dotnet/visual-basic/language-reference/error-messages/bc42358)

# 二、 异步编程工作原理

C# 中的async和await关键字是异步编程的核心。通过使用这两个关键字，您可以使用 .NET Framework、.NET Core 或 Windows 运行时中的资源来创建异步方法，几乎与创建同步方法一样轻松。使用关键字定义的异步方法称为异步方法，如以下方法

```
public async Task<int> GetUrlContentLengthAsync()
{
    var client = new HttpClient();

    Task<string> getStringTask =
        client.GetStringAsync("https://learn.microsoft.com/dotnet");

    DoIndependentWork();

    string contents = await getStringTask;

    return contents.Length;
}

void DoIndependentWork()
{
    Console.WriteLine("Working...");
}
```

从方法签名开始。它包括 async 修饰符。返回类型为 Task<int> ，方法名称以 Async 在方法主体中， GetStringAsync 返回一个 Task<string> . 这意味着当您完成 await 任务时，您将得到一个 string （ contents  ）。在等待任务之前，以执行不依赖于 string from 的工作 GetStringAsync 

密切注意操作 await 员。它暂停 GetUrlContentLengthAsync ：

*   GetUrlContentLengthAsync getStringTask完成之前无法继续
*   同时，控制权返回给 的调用者 GetUrlContentLengthAsync
*   getStringTask 完成后，控制将在此处恢复
*   然后操作员从 中await 检索结果。 string getStringTask

return 语句指定一个整数结果。任何正在等待 GetUrlContentLengthAsync 检索长度值的方法

如果 GetUrlContentLengthAsync 在调用和等待其完成之间没有任何可以执行的工作 GetStringAsync ，您可以通过在以下单个语句中调用和等待来简化代码

```
string contents = await client.GetStringAsync("https://learn.microsoft.com/dotnet");
```

以下特征总结了前面的示例成为异步方法的原因：

*   方法签名包括修饰符 async 

*   按照惯例，异步方法的名称以“Async”后缀结尾

*   返回类型是以下类型之一：

    *   [Task<TResult>](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task-1)如果您的方法有一个 return 语句，其中操作数的类型为 TResult
    *   如果您的方法没有 return 语句或有一个没有操作数的 return 语句，则[执行任务](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task)
    *   void 如果您正在编写异步事件处理程序
    *   具有方法的任何其他类型 GetAwaiter
*   该方法通常至少包含一个 await 表达式，该表达式标记该方法在等待的异步操作完成之前无法继续的点。同时，该方法被挂起，控制权返回到该方法的调用者

在异步方法中，您使用提供的关键字和类型来指示您想要执行的操作，编译器将完成其余的工作，包括跟踪当控制权返回到挂起方法中的等待点时必须发生的情况。一些例行过程（例如循环和异常处理）可能很难在传统的异步代码中处理。在异步方法中，您可以像在同步解决方案中一样编写这些元素，然后问题就解决了

在异步编程中控制流的执行
![image.png](https://upload-images.jianshu.io/upload_images/29476859-3590232852354112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图中的数字对应于以下步骤，当调用方法调用异步方法时启动

1.  调用方法调用并等待 GetUrlContentLengthAsync 异步方法

2.   GetUrlContentLengthAsync 创建[HttpClient](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httpclient)实例并调用[GetStringAsync](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httpclient.getstringasync)异步方法以将网站内容下载为字符串

3.  发生了一些事情， GetStringAsync 暂停了它的进展。也许它必须等待网站下载或其他一些阻止活动。为了避免阻塞资源，  GetStringAsync 将控制权交给其调用者 GetUrlContentLengthAsync 

     GetStringAsync 返回一个Task<TResult>，其中 TResult 是一个字符串，并将 GetUrlContentLengthAsync 任务分配给 getStringTask 变量。该任务表示调用 的正在进行的过程 GetStringAsync ，并承诺在工作完成时生成实际的字符串值

4.  因为 getStringTask 还没有等待， GetUrlContentLengthAsync 可以继续进行其他不依赖于最终结果的工作 GetStringAsync 。该工作由对同步方法的调用来表示 DoIndependentWork 

5.   DoIndependentWork 是一个同步方法，它完成其工作并返回给其调用者。

6.   GetUrlContentLengthAsync 已经用完了它可以做的工作，但没有结果 getStringTask 。GetUrlContentLengthAsync next 想要计算并返回下载的字符串的长度，但该方法在获得该字符串之前无法计算该值
因此，GetUrlContentLengthAsync 使用 await 运算符来暂停其进度并将控制权交给调用的方法  GetUrlContentLengthAsync。 GetUrlContentLengthAsync 返回 aTask<int>给调用者。该任务代表一个承诺，即生成一个整数结果，该结果是下载的字符串的长度

 **如果 GetStringAsync （因此）在等待它 getStringTask 之前完成，则控制权仍保留在 中。如果调用的异步进程已经完成并且不必等待最终结果，则挂起然后返回的开销将被浪费 `GetUrlContentLengthAsync` `GetUrlContentLengthAsync` `GetUrlContentLengthAsync` `getStringTask` `GetUrlContentLengthAsync`**

在调用方法内部，处理模式继续。在等待该结果之前，调用者可能会执行不依赖于结果的其他工作 GetUrlContentLengthAsync ，或者调用者可能会立即等待。调用方法正在等待 GetUrlContentLengthAsync ，并且 GetUrlContentLengthAsync 正在等待 GetStringAsync 

7.  GetStringAsync 完成并产生一个字符串结果，调用不会 GetStringAsync 以您期望的方式返回字符串结果。（请记住，该方法已在步骤 3 中返回了一个任务。）相反，字符串结果存储在代表该方法完成的任务中 getStringTask 。await 运算符从 中检索结果 getStringTask 。赋值语句将检索到的结果赋给 contents 

8.  当 GetUrlContentLengthAsync 得到字符串结果时，该方法可以计算字符串的长度。那么 的工作 GetUrlContentLengthAsync 也完成了，等待的事件处理程序可以恢复。同步方法在其工作完成时返回（步骤 5），而异步方法在其工作挂起时返回任务值（步骤 3 和 6）。当异步方法最终完成其工作时，任务将被标记为已完成，并且结果（如果有）将存储在任务中


# 三、 用例

##### 1. 举例一

假设需要从网络上下载一些数据，但下载的过程可能需要一段时间，为了不阻塞 UI 线程，以免影响用户的使用体验，可以使用异步方法来执行下载操作。例如：

```
async Task DownloadDataAsync()
{
    // 创建用于下载数据的 HttpClient 实例
    var client = new HttpClient();

    // 发送 HTTP 请求，并等待响应结果
    var response = await client.GetAsync("http://example.com/data");

    // 读取响应结果，并将数据保存到文件中
    var data = await response.Content.ReadAsStringAsync();
    File.WriteAllText("data.txt", data);
 }
```

在上述代码中，使用 async 关键字定义了一个名为 DownloadDataAsync 的异步方法，它会从网络上下载数据并保存到文件中

在方法中，首先创建了一个 HttpClient 实例，然后使用它发送 HTTP 请求，等待响应结果

在等待响应结果的过程中，异步方法会暂停执行，并允许其他任务继续执行。当响应结果返回后，使用 await 关键字读取响应结果并将数据保存到文件中

##### 2. 举例二

以制作早餐的指令示例来了解和 async 关键字 await 一系列异步指令的代码：
+ 倒一杯咖啡
+ 热锅，然后煎两个鸡蛋
+ 煎三片培根
+ 烤两片面包
+ 在吐司中加入黄油和果酱
+ 倒入一杯橙汁


(1)  如果按照顺序执行的每一个任务的话，计算机会阻塞每一个语句，直到一个任务完成才会进行下一个任务，这样累加起来的时长要耗费较长的时间

![image.png](https://upload-images.jianshu.io/upload_images/29476859-035106ac302019dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(2) 如果使用异步，就不会阻塞任务，而是会等待任务

```
static async Task Main(string[] args)
{
    Coffee cup = PourCoffee();
    Console.WriteLine("coffee is ready");

    Egg eggs = await FryEggsAsync(2);
    Console.WriteLine("eggs are ready");

    Bacon bacon = await FryBaconAsync(3);
    Console.WriteLine("bacon is ready");

    Toast toast = await ToastBreadAsync(2);
    ApplyButter(toast);
    ApplyJam(toast);
    Console.WriteLine("toast is ready");

    Juice oj = PourOJ();
    Console.WriteLine("oj is ready");
    Console.WriteLine("Breakfast is ready!");
}
```

为了让线程在任务运行时不会阻塞。await关键字提供了一种非阻塞方式来启动任务。这上述段代码中，每个异步操作都是依次执行的。在执行完一个异步操作后，才会执行下一个异步操作。例如，先执行 FryEggsAsync 方法，等待其完成后再执行 FryBaconAsync 方法，再等待其完成后再执行 ToastBreadAsync 方法。这种方式可以保证异步操作的执行顺序，但可能会增加等待时间，降低程序的效率

将上述代码调整为

```
Coffee cup = PourCoffee();
Console.WriteLine("Coffee is ready");

Task<Egg> eggsTask = FryEggsAsync(2);
Task<Bacon> baconTask = FryBaconAsync(3);
Task<Toast> toastTask = ToastBreadAsync(2);

Toast toast = await toastTask;
ApplyButter(toast);
ApplyJam(toast);
Console.WriteLine("Toast is ready");
Juice oj = PourOJ();
Console.WriteLine("Oj is ready");

Egg eggs = await eggsTask;
Console.WriteLine("Eggs are ready");
Bacon bacon = await baconTask;
Console.WriteLine("Bacon is ready");

Console.WriteLine("Breakfast is ready!");
```

（3) 异步操作后跟同步工作的组合是异步操作

前面的代码向您展示了可以使用[Task](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task)或[Task<TResult>](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task-1)对象来保存正在运行的任务。在使用 await 每项任务的结果之前。下一步是创建代表其他工作组合的方法。在供应早餐之前，您需要等待表示在添加黄油和果酱之前烘烤面包的任务。您可以使用以下代码来表示该工作：

```
static async Task<Toast> MakeToastWithButterAndJamAsync(int number)
{
    var toast = await ToastBreadAsync(number);
    ApplyButter(toast);
    ApplyJam(toast);

    return toast;
}
```
此时，三个异步操作是同时启动的。在启动异步操作后，使用 await 关键字等待异步操作的完成。由于异步操作是同时启动的，因此可以减少等待时间，提高程序的效率

但是需要注意的是，异步操作的完成顺序可能是不确定的，因此需要在使用异步操作的结果时进行判断，避免出现错误

此段代码中，Egg 和 Bacon 的异步任务是并行执行的，而ToastBreadAsync 方法完成后，才会执行 ApplyButter 和 ApplyJam 方法。这种方式可以保证 ApplyButter 和 ApplyJam 方法的正确执行顺序，避免出现错误

这时我们就要提及一个一个重要概念：

异步操作后跟同步工作的组合是异步操作。换句话说，如果操作的任何部分是异步的，则整个操作也是异步的

```
static async Task Main(string[] args)
{
    Coffee cup = PourCoffee();
    Console.WriteLine("coffee is ready");

    var eggsTask = FryEggsAsync(2);
    var baconTask = FryBaconAsync(3);
    var toastTask = MakeToastWithButterAndJamAsync(2);

    var eggs = await eggsTask;
    Console.WriteLine("eggs are ready");

    var bacon = await baconTask;
    Console.WriteLine("bacon is ready");

    var toast = await toastTask;
    Console.WriteLine("toast is ready");

    Juice oj = PourOJ();
    Console.WriteLine("oj is ready");
    Console.WriteLine("Breakfast is ready!");
}
```

通过将操作分离到返回任务的新方法中来组合任务。您可以选择何时等待该任务，也可以同时启动其他任务

(3) Await tasks efficiently

await可以使用类的方法来改进前面代码末尾的一系列语句Task,其中一个 API 是[WhenAll](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.whenall)，它返回一个任务[Task](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task)，当其参数列表中的所有任务都完成时，该任务完成，如以下代码所示：

```
await Task.WhenAll(eggsTask, baconTask, toastTask);
Console.WriteLine("Eggs are ready");
Console.WriteLine("Bacon is ready");
Console.WriteLine("Toast is ready");
Console.WriteLine("Breakfast is ready!");
```

另一种选择是使用[WhenAny](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.whenany)，它返回一个 Task<Task> 在其任何参数完成时完成的值。您可以等待返回的任务，知道它已经完成。以下代码显示如何使用[WhenAny](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.whenany)等待第一个任务完成，然后处理其结果。处理已完成任务的结果后，您可以从传递给 的任务列表中删除该已完成任务 WhenAny 

```
var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
while (breakfastTasks.Count > 0)
{
    Task finishedTask = await Task.WhenAny(breakfastTasks);
    if (finishedTask == eggsTask)
    {
        Console.WriteLine("Eggs are ready");
    }
    else if (finishedTask == baconTask)
    {
        Console.WriteLine("Bacon is ready");
    }
    else if (finishedTask == toastTask)
    {
        Console.WriteLine("Toast is ready");
    }
    await finishedTask;
    breakfastTasks.Remove(finishedTask);
}
```

快结束时，您会看到这条线await finishedTask;该线路await Task.WhenAny不会等待完成的任务。它await是Task由 返回的Task.WhenAny。的结果Task.WhenAny是已完成（或发生故障）的任务。await即使您知道该任务已完成运行，您也应该再次执行该任务。这就是您检索其结果或确保抛出导致其故障的异常的方式

经过所有这些更改后，代码的最终版本如下所示：

```
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace AsyncBreakfast
{
    // These classes are intentionally empty for the purpose of this example. They are simply marker classes for the purpose of demonstration, contain no properties, and serve no other purpose.
    internal class Bacon { }
    internal class Coffee { }
    internal class Egg { }
    internal class Juice { }
    internal class Toast { }

    class Program
    {
        static async Task Main(string[] args)
        {
            Coffee cup = PourCoffee();
            Console.WriteLine("coffee is ready");

            var eggsTask = FryEggsAsync(2);
            var baconTask = FryBaconAsync(3);
            var toastTask = MakeToastWithButterAndJamAsync(2);

            var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
            while (breakfastTasks.Count > 0)
            {
                Task finishedTask = await Task.WhenAny(breakfastTasks);
                if (finishedTask == eggsTask)
                {
                    Console.WriteLine("eggs are ready");
                }
                else if (finishedTask == baconTask)
                {
                    Console.WriteLine("bacon is ready");
                }
                else if (finishedTask == toastTask)
                {
                    Console.WriteLine("toast is ready");
                }
                await finishedTask;
                breakfastTasks.Remove(finishedTask);
            }

            Juice oj = PourOJ();
            Console.WriteLine("oj is ready");
            Console.WriteLine("Breakfast is ready!");
        }

        static async Task<Toast> MakeToastWithButterAndJamAsync(int number)
        {
            var toast = await ToastBreadAsync(number);
            ApplyButter(toast);
            ApplyJam(toast);

            return toast;
        }

        private static Juice PourOJ()
        {
            Console.WriteLine("Pouring orange juice");
            return new Juice();
        }

        private static void ApplyJam(Toast toast) =>
            Console.WriteLine("Putting jam on the toast");

        private static void ApplyButter(Toast toast) =>
            Console.WriteLine("Putting butter on the toast");

        private static async Task<Toast> ToastBreadAsync(int slices)
        {
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("Putting a slice of bread in the toaster");
            }
            Console.WriteLine("Start toasting...");
            await Task.Delay(3000);
            Console.WriteLine("Remove toast from toaster");

            return new Toast();
        }

        private static async Task<Bacon> FryBaconAsync(int slices)
        {
            Console.WriteLine($"putting {slices} slices of bacon in the pan");
            Console.WriteLine("cooking first side of bacon...");
            await Task.Delay(3000);
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("flipping a slice of bacon");
            }
            Console.WriteLine("cooking the second side of bacon...");
            await Task.Delay(3000);
            Console.WriteLine("Put bacon on plate");

            return new Bacon();
        }

        private static async Task<Egg> FryEggsAsync(int howMany)
        {
            Console.WriteLine("Warming the egg pan...");
            await Task.Delay(3000);
            Console.WriteLine($"cracking {howMany} eggs");
            Console.WriteLine("cooking the eggs ...");
            await Task.Delay(3000);
            Console.WriteLine("Put eggs on plate");

            return new Egg();
        }

        private static Coffee PourCoffee()
        {
            Console.WriteLine("Pouring coffee");
            return new Coffee();
        }
    }
}
```

![image.png](https://upload-images.jianshu.io/upload_images/29476859-d7fc2f5029edcd84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ps: 

异步的相关知识： https://learn.microsoft.com/en-us/dotnet/visual-basic/programming-guide/concepts/async/

WhenAny: https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.whenany?view=net-8.0

WhenAll:https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.whenall?view=net-8.0
