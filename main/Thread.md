# 一、 进程和线程的关系

进程和线程是现代网络操作系统的核心概念

#### 1. 进程
进程是一个具有一定独立功能的程序在一个数据集合上依次动态执行的过程。进程是一个正在执行的程序的实例，包括程序计数器、寄存器和程序变量的当前值。简而言之：进程时一个执行中的应用程序，进程时cpu调度的最小单位，是应用程序的执行实例

#### 2. 线程（[Thread](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread?view=net-8.0)）
线程是轻量级进程，被定义为程序的执行路径，同一个进程中的线程共享进程的资源，线程是处理器（CPU）任务调度和执行的基本单位。每个线程都定义了一个独特的控制流。如果应用程序涉及到复杂的和耗时的操作，那么设置不同的线程执行路径往往是有益的

以下图片可以清晰呈现 操作系统，进程，线程，内存之间的关系

![image.png](https://upload-images.jianshu.io/upload_images/29476859-dc31bdfc06e968c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3. 进程和线程的关系

（1）一个线程只能属于一个进程，而一个进程可以有多个线程，但至少有一个线程

（2）资源分配给进程，同一进程的所有线程共享该进程的所有资源

（3）处理机分给线程，即真正在处理机上运行的是线程

（4）线程在执行过程中，需要协作同步。不同进程的线程间要利用消息通信的办法实现同步。线程是指进程内的一个执行单元，也是进程内的可调度实体

#### 4. 进程与线程的区别

（1）调度：线程作为调度和分配的基本单位，进程作为拥有资源的基本单位

（2）并发性：不仅进程之间可以并发执行，同一个进程的多个线程之间也可并发执行

（3）拥有资源：进程是拥有资源的一个独立单位，线程不拥有系统资源，但可以访问隶属于进程的资源。即进程间相互独立，同一进程的各线程间资源共享

（4）系统开销：在创建或撤消进程时，由于系统都要为之分配和回收资源，导致系统的开销明显大于创建或撤消线程时的开销

#### 5. 进程间通信的方式

（1）管道（pipe）及有名管道（named pipe）：管道可用于具有亲缘关系的父子进程间的通信，有名管道除了具有管道所具有的功能外，它还允许无亲缘关系进程间的通信

（2）信号（signal）：信号是在软件层次上对中断机制的一种模拟，它是比较复杂的通信方式，用于通知进程有某事件发生，一个进程收到一个信号与处理器收到一个中断请求效果上可以说是一致的

（3）消息队列（message queue）：消息队列是消息的链接表，它克服了上两种通信方式中信号量有限的缺点，具有写权限得进程可以按照一定得规则向消息队列中添加新信息；对消息队列有读权限得进程则可以从消息队列中读取信息

（4）共享内存（shared memory）：可以说这是最有用的进程间通信方式。它使得多个进程可以访问同一块内存空间，不同进程可以及时看到对方进程中对共享内存中数据得更新。这种方式需要依靠某种同步操作，如互斥锁和信号量等

（5）信号量（semaphore）：主要作为进程之间及同一种进程的不同线程之间得同步和互斥手段

（6）套接字（socket）：这是一种更为一般得进程间通信机制，它可用于网络中不同机器之间的进程间通信，应用非常广泛

# 二、线程的生命周期

由于线程之间的相互制约，致使线程在运行中呈现出间断性

线程生命周期开始于 System.Threading.Thread 类的对象被创建时，结束于线程被终止或完成执行时

下面列出了线程生命周期中的各种状态：
+ New 初始化状态：当线程实例被创建但 Start 方法未被调用时的状况，仅仅在堆区中被分配了内存。
+ Runnable 就绪状态：当线程准备好运行并等待 CPU 周期时的状况
    + 当调用线程对象的start()方法，线程即进入就绪状态
    + 处于就绪状态的线程，只是说明此线程已经做好了准备，虚拟机会为它创建方法调用栈和程序计数器。
    +  处于这个状态的线程位于可运行池中，等待获得CPU的使用权，并不是说执行了start()此线程立即就会执行。
+ Running 运行状态： 当就绪状态中的线程获得了CUP执行资源，执行run()中的代码
+ Blocked 阻塞状态：处于运行中的线程，由于某种原因放弃对cpu的使用权，处于阻塞状态，直到其进入就绪状态，才有机会再次被cpu调用进入运行状态。
    + 等待阻塞：运行状态中的线程执行SpinWait方法，进入等待队列，等待阻塞；虚拟机就会把线程放到这个对象的等待池中
    + 同步阻塞：线程获取同步锁失败（因为锁被其他线程占用），虚拟机就会把这个线程放到这个对象的锁池中
    + 其他阻塞：通过调用sleep方法或者join方法或者发出I/O请求时，线程会进入阻塞状态，当sleep()状态超时，或者join()等待线程终止或者超时，或者I/O处理完毕，线程重新转入就绪状态
+ Terminated 终止状态：当线程已完成执行或已中止时的状况
    + 正常结束，线程执行完
    + 异常退出，除了程序有问题导致的异常的退出，还可以使用共享变量的方式（定义个boolean标识等）退出，或者Interrupt[中断](https://so.csdn.net/so/search?q=%E4%B8%AD%E6%96%AD&spm=1001.2101.3001.7020)线程,抛出异常，捕获异常break，跳出循环状态
    + 调用stop()，会造成死锁，线程不安全，不建议使用

![image.png](https://upload-images.jianshu.io/upload_images/29476859-a68cc6cb5bb1ce47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 三、 线程常用的属性

+ CurrentContext：获取线程正在其中执行的当前上下文
+ CurrentCulture：获取或设置当前线程的区域性
+ CurrentPrincipal：获取或设置线程的当前负责人（对基于角色的安全性而言）
+ CurrentThread	：获取当前正在运行的线程
+ CurrentUICulture：获取或设置资源管理器使用的当前区域性以便在运行时查找区域性特定的资源
+ ExecutionContext：获取一个 ExecutionContext 对象，该对象包含有关当前线程的各种上下文的信息
+ IsAlive：获取一个值，该值指示当前线程的执行状态
+ IsBackground：获取或设置一个值，该值指示某个线程是否为后台线程
+ IsThreadPoolThread：获取一个值，该值指示线程是否属于托管线程池
+ ManagedThreadId：获取当前托管线程的唯一标识符
+ Name：获取或设置线程的名称
+ Priority：获取或设置一个值，该值指示线程的调度优先级
+ ThreadState：获取一个值，该值包含当前线程的状态

# 四、 线程的方法

#### 1. 线程的基本方法

##### 线程运行（[Start](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.start?view=net-8.0)）
调用该方法可以线程被安排执行，将当前实例的状态更改为 Running 运行状态

```
[System.Runtime.Versioning.UnsupportedOSPlatform("browser")]//为开发人员提供了一种标记和识别在特定操作系统平台上不受支持的方法或类型的方式
public void Start ();  // 无参

public void Start (Object obj); // 有参 或者可选择提供一个包含线程执行的方法要使用的数据的对象

```

##### 线程等待（[SpinWait](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.spinwait?view=net-8.0)）
调用该方法，线程进入 Blocked 阻塞状态，只有等待另外的线程通知或被中断才会返回，调用SpinWait()后，会释放对象锁，因为SpinWait方法一般用在同步方法或者同步代码块中

```
public static void SpinWait (int iterations);  // 使线程等待参数定义的次数iterations
```

##### 线程睡眠（[Sleep](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.sleep?view=net-8.0)）
强迫线程睡 N毫秒，Sleep不会释放当前锁，导致线程进入 Blocked 阻塞状态
    + 参数的值millisecondsTimeout为零：线程会将其剩余的时间片让给任何一个具有相同优先级且准备运行的线程。如果没有其他具有相同优先级且准备运行的线程，当前线程的执行不会被挂起
    +  millisecondsTimeout 参数的值不为零：当前线程会被挂起（即暂停执行）指定的毫秒数。在这段时间内，当前线程不会占用 CPU 时间片，并且不会继续执行代码。挂起的线程会在等待时间结束后重新进入就绪状态，等待系统调度来恢复执行
    + 总之，在调用线程的挂起操作时，通过指定合适的超时时间可以控制线程的行为。如果超时时间为零，线程会主动放弃时间片给其他具有相同优先级且准备运行的线程。如果超时时间不为零，线程会被挂起指定的时间，并在等待时间结束后重新进入就绪状态。
  + 可以为参数指定[Timeout.InfiniteTimeSpan](https://learn.microsoft.com/en-us/dotnet/api/system.threading.timeout.infinitetimespan?view=net-8.0#system-threading-timeout-infinitetimespan)`timeout`来无限期地挂起线程
  + Sleep的这种重载使用了 中的整毫秒总数`timeout`。小数毫秒将被丢弃。

```
 public static void Sleep (int millisecondsTimeout); // millisecondsTimeout 将当前线程挂起指定的毫秒数
```
  

##### 线程让步（[Yield](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.yield?view=net-8.0)）
调用该方法会让 线程将执行 让步 给准备在当前处理器上运行的另一个线程，操作系统选择要屈服的线程
Yield会使当前线程让出cpu执行时间片，与其他线程一起重新竞争cpu时间片，一般情况下，优先级高的先得到，但也不一定，有的系统对优先级不敏感
    + 如果此方法成功，则会产生线程当前时间片的其余部分。操作系统根据调用线程的优先级和可运行的其他线程的状态，为另一个时间片安排调用线程
    + 屈服仅限于正在执行调用线程的处理器。操作系统不会将执行切换到另一个处理器，即使该处理器处于空闲状态或正在运行较低优先级的线程。如果当前处理器上没有其他线程准备好执行，则操作系统不会让出执行，并且此方法返回false
```
public static bool Yield ();  // return的值是布尔类型， true 代表操作系统将执行切换到另一个线程，false 不切换
```  

##### 线程阻塞（[Join](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.join?view=net-8.0)）（等待其他线程终止）
当前线程调用Join（），则线程转为阻塞状态，阻止调用线程，直到该实例表示的线程终止
    + eg：A线程中插入了B.Join()，则B先执行，执行完，A线程继续执行
    + 常见的是主线程生成并启动了子线程，需要用到子线程返回结果的场景
    + Join是一种同步方法，它会阻塞调用线程（即调用该方法的线程），直到调用其Join方法的线程完成为止。使用此方法可确保线程已终止。如果线程不终止，调用者将无限期地阻塞
    + 如果调用Join时线程已经终止，则该方法立即返回

```
public void Join ();  // 阻止调用线程，直到该实例表示的线程终止，同时继续执行标准 COM 和SendMessage泵送
public void Join (Int32);  // 阻止调用线程，直到该实例表示的线程终止或指定的时间过去，同时继续执行标准 COM 和 SendMessage 泵送
public void Join (TimeSpan);  // 阻止调用线程，直到该实例表示的线程终止或指定的时间过去，同时继续执行标准 COM 和 SendMessage 泵送
```

##### 线程终止（[Abort](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.abort?view=net-8.0)）
调用此方法的线程上引发 ThreadAbortException，以开始终止此线程的过程。调用此方法通常会终止线程

```
public void Abort ();  

public void Abort (Object obj);  // obj 是提供有关线程终止的异常信息
```
![image.png](https://upload-images.jianshu.io/upload_images/29476859-b8da76c43f1ecccf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/29476859-c60139800d689664.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2. 其他方法

+ AllocateDataSlot
在所有的线程上分配未命名的数据槽。为了获得更好的性能，请改用以 ThreadStaticAttribute 属性标记的字段
```
public static LocalDataStoreSlot AllocateDataSlot()
```

+ AllocateNamedDataSlot
在所有线程上分配已命名的数据槽。为了获得更好的性能，请改用以 ThreadStaticAttribute 属性标记的字段。

```
public static LocalDataStoreSlot AllocateNamedDataSlot( string name)
```

+ BeginCriticalRegion
通知主机执行将要进入一个代码区域，在该代码区域内线程中止或未经处理的异常的影响可能会危害应用程序域中的其他任务

```
public static void BeginCriticalRegion()
```

+ BeginThreadAffinity
通知主机托管代码将要执行依赖于当前物理操作系统线程的标识的指令

```
public static void BeginThreadAffinity()
```

+ EndCriticalRegion
通知主机执行将要进入一个代码区域，在该代码区域内线程中止或未经处理的异常仅影响当前任务

```
public static void EndCriticalRegion()
```

+ EndThreadAffinity
通知主机托管代码已执行完依赖于当前物理操作系统线程的标识的指令

```
public static void EndThreadAffinity()
```

+ FreeNamedDataSlot
为进程中的所有线程消除名称与槽之间的关联。为了获得更好的性能，请改用以 ThreadStaticAttribute 属性标记的字段。

```
public static void FreeNamedDataSlot(string name)
```

+ GetData
在当前线程的当前域中从当前线程上指定的槽中检索值。为了获得更好的性能，请改用以 ThreadStaticAttribute 属性标记的字段

```
public static Object GetData( LocalDataStoreSlot slot )
```

+ AppDomain
返回当前线程正在其中运行的当前域

```
public static AppDomain GetDomain()
```

+ GetDomainID
返回唯一的应用程序域标识符

```
public static AppDomain GetDomainID()
```

+ GetNamedDataSlot
查找已命名的数据槽。为了获得更好的性能，请改用以 ThreadStaticAttribute 属性标记的字段

```
public static LocalDataStoreSlot GetNamedDataSlot( string name )
```

+ Interrupt
中断处于 WaitSleepJoin 线程状态的线程

```
public void Interrupt()
```

+ MemoryBarrier
按如下方式同步内存存取：执行当前线程的处理器在对指令重新排序时，不能采用先执行 MemoryBarrier 调用之后的内存存取，再执行 MemoryBarrier 调用之前的内存存取的方式

```
public static void MemoryBarrier()
```

+ ResetAbort
取消为当前线程请求的 Abort

```
public static void ResetAbort()
```

+ SetData
在当前正在运行的线程上为此线程的当前域在指定槽中设置数据。为了获得更好的性能，请改用以 ThreadStaticAttribute 属性标记的字段

```
public static void SetData( LocalDataStoreSlot slot, Object data )
```

+ VolatileRead
读取字段值。无论处理器的数目或处理器缓存的状态如何，该值都是由计算机的任何处理器写入的最新值。此方法有不同的重载形式。这里只给出了一些形式
```
public static byte VolatileRead( ref byte address )
public static double VolatileRead( ref double address )
public static int VolatileRead( ref int address )
public static Object VolatileRead( ref Object address )
```

+ VolatileWrite
立即向字段写入一个值，以使该值对计算机中的所有处理器都可见。此方法有不同的重载形式。这里只给出了一些形式
```
public static void VolatileWrite( ref byte address, byte value )
public static void VolatileWrite( ref double address, double value )
public static void VolatileWrite( ref int address, int value )
public static void VolatileWrite( ref Object address, Object value )
```

# 五、 线程的用例

##### 1. 创建线程

线程是通过扩展 Thread 类创建的。扩展的 Thread 类调用 Start() 方法来开始子线程的执行

```
using System;
using System.Threading;

namespace MultithreadingApplication
{
    class ThreadCreationProgram
    {
        public static void CallToChildThread()
        {
            Console.WriteLine("Child thread starts");
        }
       
        static void Main(string[] args)
        {
            ThreadStart childref = new ThreadStart(CallToChildThread);
            Console.WriteLine("In Main: Creating the Child thread");
            Thread childThread = new Thread(childref);
            childThread.Start();
            Console.ReadKey();
        }
    }
}

/* 运行结果
In Main: Creating the Child thread
Child thread starts
*/
```

##### 2. 管理线程

Thread 类提供了各种管理线程的方法，下面的实例演示了 sleep() 方法的使用，用于在一个特定的时间暂停线程

```
using System;
using System.Threading;

namespace MultithreadingApplication
{
    class ThreadCreationProgram
    {
        public static void CallToChildThread()
        {
            Console.WriteLine("Child thread starts");
            // 线程暂停 5000 毫秒
            int sleepfor = 5000; 
            Console.WriteLine("Child Thread Paused for {0} seconds", 
                              sleepfor / 1000);
            Thread.Sleep(sleepfor);
            Console.WriteLine("Child thread resumes");
        }
        
        static void Main(string[] args)
        {
            ThreadStart childref = new ThreadStart(CallToChildThread);
            Console.WriteLine("In Main: Creating the Child thread");
            Thread childThread = new Thread(childref);
            childThread.Start();
            Console.ReadKey();
        }
    }
}

/* 运行结果
In Main: Creating the Child thread
Child thread starts
Child Thread Paused for 5 seconds
Child thread resumes
*/
```

##### 3. 销毁线程

Abort() 方法用于销毁线程

通过抛出 threadabortexception 在运行时中止线程。这个异常不能被捕获，如果有 finally 块，控制会被送至 finally 块

```
using System;
using System.Threading;

namespace MultithreadingApplication
{
    class ThreadCreationProgram
    {
        public static void CallToChildThread()
        {
            try
            {

                Console.WriteLine("Child thread starts");
                // 计数到 10
                for (int counter = 0; counter <= 10; counter++)
                {
                    Thread.Sleep(500);
                    Console.WriteLine(counter);
                }
                Console.WriteLine("Child Thread Completed");

            }
            catch (ThreadAbortException e)
            {
                Console.WriteLine("Thread Abort Exception");
            }
            finally
            {
                Console.WriteLine("Couldn't catch the Thread Exception");
            }

        }
        
        static void Main(string[] args)
        {
            ThreadStart childref = new ThreadStart(CallToChildThread);
            Console.WriteLine("In Main: Creating the Child thread");
            Thread childThread = new Thread(childref);
            childThread.Start();
            // 停止主线程一段时间
            Thread.Sleep(2000);
            // 现在中止子线程
            Console.WriteLine("In Main: Aborting the Child thread");
            childThread.Abort();
            Console.ReadKey();
        }
    }
}

/* 运行结果
In Main: Creating the Child thread
Child thread starts
0
1
2
In Main: Aborting the Child thread
Thread Abort Exception
Couldn't catch the Thread Exception 
*/
```


ps：
线程的具体知识： https://learn.microsoft.com/en-us/dotnet/standard/threading/using-threads-and-threading
