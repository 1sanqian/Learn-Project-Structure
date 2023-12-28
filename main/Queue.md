# Queue 队列

队列（Queue）代表了一个先进先出的对象集合。需要对各项进行先进先出的访问时，则使用队列。在列表中添加一项，称为入队，从列表中移除一项时，称为出队

队列的基本工作原理

![image.png](https://upload-images.jianshu.io/upload_images/29476859-21a29db49a9888d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

循环队列

![image.png](https://upload-images.jianshu.io/upload_images/29476859-f662e6aa66cfeec2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 属性

+ Count：获取 Queue 中包含的元素个数

### 方法

##### 1.	Clear 移除

从 Queue 中移除所有的元素

```
public virtual void Clear();
```

##### 2.	Contains  包含

判断某个元素是否在 Queue 中

```
public virtual bool Contains( object obj );
```

##### 3.	Dequeue 移除 并返回队列开头

移除并返回在 Queue 的开头的对象

```
public virtual object Dequeue();
```

##### 4.	Enqueue 队列末尾添加一个

向 Queue 的末尾添加一个对象

```
public virtual void Enqueue( object obj );
```

##### 5.	ToArray 复制到数组

复制 Queue 到一个新的数组中

```
public virtual object[] ToArray();
```

##### 6. TrimToSize 设置容量

设置容量为 Queue 中元素的实际个数

```
public virtual void TrimToSize();
```

### 示例

```
using System;
using System.Collections;

namespace CollectionsApplication
{
   class Program
   {
      static void Main(string[] args)
      {
         Queue q = new Queue();

         q.Enqueue('A');
         q.Enqueue('M');
         q.Enqueue('G');
         q.Enqueue('W');
         
         Console.WriteLine("Current queue: ");
         foreach (char c in q)
         Console.Write(c + " ");
         Console.WriteLine();
         q.Enqueue('V');
         q.Enqueue('H');

         Console.WriteLine("Current queue: ");         
         foreach (char c in q)
         Console.Write(c + " ");
         Console.WriteLine();

         Console.WriteLine("Removing some values ");
         char ch = (char)q.Dequeue();
         Console.WriteLine("The removed value: {0}", ch);
         ch = (char)q.Dequeue();
         Console.WriteLine("The removed value: {0}", ch);
         Console.ReadKey();
      }
   }
}
/* Output
Current queue: 
A M G W 
Current queue: 
A M G W V H 
Removing values
The removed value: A
The removed value: M
*/
```

ps:

Queue 类： https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.queue?view=net-8.0
