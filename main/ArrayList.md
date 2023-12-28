# ArrayList 动态数组

动态数组（ArrayList）代表了可被单独索引的对象的有序集合，它基本上可以替代一个数组。但是，与数组不同的是，可以使用索引在指定的位置添加和移除项目，动态数组会自动重新调整它的大小。它也允许在列表中进行动态内存分配、增加、搜索、排序各项。

### 属性

+ Capacity：获取或设置 ArrayList 可以包含的元素个数
+ Count：获取 ArrayList 中实际包含的元素个数
+ IsFixedSize：获取一个值，表示 ArrayList 是否具有固定大小
+ IsReadOnly：获取一个值，表示 ArrayList 是否只读
+ IsSynchronized：获取一个值，表示访问 ArrayList 是否同步（线程安全）
+ Item[Int32]：获取或设置指定索引处的元素
+ SyncRoot：获取一个对象用于同步访问 ArrayList

### 方法

##### 1. Add 添加一个

在 ArrayList 的末尾添加一个对象

```
public virtual int Add( object value );
```

##### 2. AddRange 添加多个

在 ArrayList 的末尾添加 ICollection 的元素

```
public virtual void AddRange( ICollection c );
```

##### 3. Clear 移除

从 ArrayList 中移除所有的元素

```
public virtual void Clear();
```

##### 4. Contains 包含

判断某个元素是否在 ArrayList 中

```
public virtual bool Contains(object item);
```

##### 5. GetRange 获取子集

返回一个 ArrayList，表示源 ArrayList 中元素的子集

```
public virtual ArrayList GetRange( int index, int count );
```

##### 6. IndexOf 返回指定元素索引值

返回某个值在 ArrayList 中第一次出现的索引，索引从零开始

```
public virtual int IndexOf(object);
```

##### 7. Insert 插入一个

在 ArrayList 的指定索引处，插入一个元素

```
public virtual void Insert( int index, object value );
```

##### 8. InsertRange 插入多个

在 ArrayList 的指定索引处，插入某个集合的元素

```
public virtual void InsertRange( int index, ICollection c )；
```

##### 9. Remove 移除第一次指定对象

从 ArrayList 中移除第一次出现的指定对象

```
public virtual void Remove( object obj );
```

##### 10. RemoveAt 移除指定索引对象

移除 ArrayList 的指定索引处的元素

```
public virtual void RemoveAt( int index )
```

##### 11. RemoveRange 移除范围内多个

从 ArrayList 中移除某个范围的元素

```
public virtual void RemoveRange( int index, int count );
```

##### 12. Reverse 反转顺序

逆转 ArrayList 中元素的顺序

```
public virtual void Reverse();
```

##### 13. SetRange 复制

复制某个集合的元素到 ArrayList 中某个范围的元素上

```
public virtual void SetRange( int index, ICollection c );
```

##### 14. Sort 排序

对 ArrayList 中的元素进行排序

```
public virtual void Sort();
```

##### 15. TrimToSize 设置容量

设置容量为 ArrayList 中元素的实际个数

```
public virtual void TrimToSize();
```

### 示例

ArrayList 存储不同类型时的排序

```
using System;
using System.Collections;

namespace CollectionApp
{
    class MyComparer : IComparer
    {
        public int Compare(object x, object y)
        {
            // 自定义比较规则
            // 如果x,y都是int，那么按正常流程比较
            // 如果其中一个不是int，那么认为不是int的值小
            // 如果都不是int，那么认为他们相等
            if (x is int)
            {
                if (y is int)
                {
                    if ((int)x < (int)y) return -1;
                    if ((int)x == (int)y) return 0;
                    return 1;
                }
                return 1;
            }
            if (y is int) return -1;
                return 0;
        }
    }

    class Entry
    {
        static void Print(ArrayList l)
        {
            Console.WriteLine("\t大小能力：{0}   实际大小：{1}", l.Capacity, l.Count);
            Console.Write("\t内容：");
            foreach (object obj in l)
            Console.Write("{0} ", obj);
            Console.Write("\r\n");
        }
        static void Main()
        {
            ArrayList l = new ArrayList();
            Console.WriteLine("添加一些不都是Int的数据：");
            l.Add(22);
            l.Add(77);
            l.Add("abc");
            l.Add(33);
            Print(l);
            Console.WriteLine("排序：");
            l.Sort(new MyComparer());
            Print(l);
            Console.ReadKey();
        }
    }
}

/* Output
添加一些不都是Int的数据：
    大小能力：4   实际大小：4
    内容：22 77 abc 33 
排序：
    大小能力：4   实际大小：4
    内容：abc 22 33 77 
*/
```

ps:

ArrayList 类： https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.arraylist?view=net-8.0
