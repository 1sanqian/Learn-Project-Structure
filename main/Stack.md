# Stack 堆栈

堆栈（Stack）代表了一个后进先出的对象集合。需要对各项进行后进先出的访问时，则使用堆栈。在列表中添加一项，称为推入元素，从列表中移除一项时，称为弹出元素

![image.png](https://upload-images.jianshu.io/upload_images/29476859-7605c2b33bcfce68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

栈的入栈操作

![20191212190125865.gif](https://upload-images.jianshu.io/upload_images/29476859-534e1423668e8656.gif?imageMogr2/auto-orient/strip)

栈的出栈操作

![20191212204731172.gif](https://upload-images.jianshu.io/upload_images/29476859-9a8a454031edf8b5.gif?imageMogr2/auto-orient/strip)

堆栈的基本原理图

![image.png](https://upload-images.jianshu.io/upload_images/29476859-be12b50e8d9b787d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 属性

+ Count：获取 Stack 中包含的元素个数

### 方法

##### 1. Clear 移除

从 Stack 中移除所有的元素

```
public virtual void Clear();
```

##### 2. Contains 包含

判断某个元素是否在 Stack 中

```
public virtual bool Contains( object obj );
```

##### 3. Peek	 返回顶部对象

返回在 Stack 的顶部的对象，但不移除它

```
public virtual object Peek();
```

##### 4. Pop 移除并返回顶部对象

移除并返回在 Stack 的顶部的对象

```
public virtual object Pop();
```

##### 5. Push 向顶部添加对象

向 Stack 的顶部添加一个对象

```
public virtual void Push( object obj );
```

##### 6. ToArray 复制到数组

复制 Stack 到一个新的数组中

```
public virtual object[] ToArray();
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
            Stack st = new Stack();

            st.Push('A');
            st.Push('M');
            st.Push('G');
            st.Push('W');
            
            Console.WriteLine("Current stack: ");
            foreach (char c in st)
            {
                Console.Write(c + " ");
            }
            Console.WriteLine();
            
            st.Push('V');
            st.Push('H');
            Console.WriteLine("The next poppable value in stack: {0}", 
            st.Peek());
            Console.WriteLine("Current stack: ");           
            foreach (char c in st)
            {
               Console.Write(c + " ");
            }
            Console.WriteLine();

            Console.WriteLine("Removing values ");
            st.Pop();
            st.Pop();
            st.Pop();
            
            Console.WriteLine("Current stack: ");
            foreach (char c in st)
            {
               Console.Write(c + " "); 
            }
        }
    }
}
/* Output
Current stack: 
W G M A
The next poppable value in stack: H
Current stack: 
H V W G M A
Removing values
Current stack: 
G M A
*/
```

ps:

Stack 类: https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.stack?view=net-8.0
