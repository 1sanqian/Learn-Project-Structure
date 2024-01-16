# SortedList 排序列表

SortedList 类代表了一系列按照键来排序的键/值对，这些键值对可以通过键和索引来访问

排序列表是数组和哈希表的组合。它包含一个可使用键或索引访问各项的列表。如果您使用索引访问各项，则它是一个动态数组（ArrayList），如果使用键访问各项，则它是一个哈希表（Hashtable）,集合中的各项总是按键值排序

### 属性

+ Capacity：获取或设置 SortedList 的容量
+ Count：获取 SortedList 中的元素个数
+ IsFixedSize：获取一个值，表示 SortedList 是否具有固定大小
+ IsReadOnly：获取一个值，表示 SortedList 是否只读
+ Item：获取或设置与 SortedList 中指定的键相关的值
+ Keys：获取 SortedList 中的键
+ Values：获取 SortedList 中的值

### 方法

##### 1. Add 添加一个

向 SortedList 添加一个带有指定的键和值的元素

```
public virtual void Add( object key, object value );
```

##### 2. Clear 移除

从 SortedList 中移除所有的元素

```
public virtual void Clear();
```

##### 3. ContainsKey 包含键

判断 SortedList 是否包含指定的键

```
public virtual bool ContainsKey( object key );
```

##### 4. ContainsValue 包含值

判断 SortedList 是否包含指定的值

```
public virtual bool ContainsValue( object value );
```

##### 5. GetByIndex 获取指定索引值

获取 SortedList 的指定索引处的值

```
public virtual object GetByIndex( int index );
```

##### 6. GetKey 获取指定索引键

获取 SortedList 的指定索引处的键

```
public virtual object GetKey( int index );
```

##### 7. GetKeyList 获取键

获取 SortedList 中的键

```
public virtual IList GetKeyList();
```

##### 8. GetValueList 获取值

获取 SortedList 中的值

```
public virtual IList GetValueList();
```

##### 9. IndexOfKey 获取指定索引
	
返回 SortedList 中的指定键的索引，索引从零开始

```
public virtual int IndexOfKey( object key );
```

##### 10. IndexOfValue 获取指定第一次索引
	
返回 SortedList 中的指定值第一次出现的索引，索引从零开始

```
public virtual int IndexOfValue( object value );
```

##### 11. Remove 移除指定键

从 SortedList 中移除带有指定的键的元素

```
public virtual void Remove( object key );
```

##### 12.RemoveAt  移除指定索引

移除 SortedList 的指定索引处的元素

```
public virtual void RemoveAt( int index );
```

##### 13. TrimToSize 设置容量

设置容量为 SortedList 中元素的实际个数

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
         SortedList sl = new SortedList();

         sl.Add("001", "Zara Ali");
         sl.Add("002", "Abida Rehman");
         sl.Add("003", "Joe Holzner");
         sl.Add("004", "Mausam Benazir Nur");
         sl.Add("005", "M. Amlan");
         sl.Add("006", "M. Arif");
         sl.Add("007", "Ritesh Saikia");

         if (sl.ContainsValue("Nuha Ali"))
         {
            Console.WriteLine("This student name is already in the list");
         }
         else
         {
            sl.Add("008", "Nuha Ali");
         }

         // 获取键的集合 
         ICollection key = sl.Keys;

         foreach (string k in key)
         {
            Console.WriteLine(k + ": " + sl[k]);
         }
      }
   }
}

/* Output
001: Zara Ali
002: Abida Rehman
003: Joe Holzner
004: Mausam Banazir Nur
005: M. Amlan 
006: M. Arif
007: Ritesh Saikia
008: Nuha Ali
*/
```

ps: 

SortedList 类： https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.sortedlist?view=net-8.0
