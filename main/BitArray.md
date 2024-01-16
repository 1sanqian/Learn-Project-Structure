# BitArray 点阵列

BitArray 类管理一个紧凑型的位值数组，它使用布尔值来表示，其中 true 表示位是开启的（1），false 表示位是关闭的（0）

需要存储位，但是事先不知道位数时，则使用点阵列，可以使用整型索引从点阵列集合中访问各项，索引从零开始

### 属性

+ Count：获取 BitArray 中包含的元素个数
+ IsReadOnly：获取一个值，表示 BitArray 是否只读
+ Item：获取或设置 BitArray 中指定位置的位的值
+ Length：获取或设置 BitArray 中的元素个数

### 方法

##### 1. And 按位与

对当前的 BitArray 中的元素和指定的 BitArray 中的相对应的元素执行按位与操作

```
public BitArray And( BitArray value );
```

##### 2. Get 获取 指定 位 的值

获取 BitArray 中指定位置的位的值

```
public bool Get( int index );
```

##### 3. Not 位值反转

把当前的 BitArray 中的位值反转，以便设置为 true 的元素变为 false，设置为 false 的元素变为 true

```
public BitArray Not();
```

##### 4. Or 按位或

对当前的 BitArray 中的元素和指定的 BitArray 中的相对应的元素执行按位或操作

```
public BitArray Or( BitArray value );
```

##### 5. Set 指定 位 设置 值

把 BitArray 中指定位置的位设置为指定的值

```
public void Set( int index, bool value );
```

##### 6. SetAll 所有 位 设置 值

把 BitArray 中的所有位设置为指定的值

```
public void SetAll( bool value );
```

##### 7. Xor 按位异或

对当前的 BitArray 中的元素和指定的 BitArray 中的相对应的元素执行按位异或操作

```
public BitArray Xor( BitArray value );
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
            // 创建两个大小为 8 的点阵列
            BitArray ba1 = new BitArray(8);
            BitArray ba2 = new BitArray(8);
            byte[] a = { 60 };  // 二进制码 0011 1100
            byte[] b = { 13 };  // 二进制码 0000 1101
            
            // 把值 60 和 13 存储到点阵列中
            ba1 = new BitArray(a);
            ba2 = new BitArray(b);

            // ba1 的内容
            Console.WriteLine("Bit array ba1: 60");
            for (int i = 0; i < ba1.Count; i++)
            {
                Console.Write("{0, -6} ", ba1[i]);
            }
            // i 从 0 开始，二进制码倒着输出： b1 : 0011 1100
            Console.WriteLine();
            
            // ba2 的内容
            Console.WriteLine("Bit array ba2: 13");
            for (int i = 0; i < ba2.Count; i++)
            {
                Console.Write("{0, -6} ", ba2[i]);
            }
            // i 从 0 开始，二进制码倒着输出： b2 : 1011 0000
            Console.WriteLine();
           
            
            BitArray ba3 = new BitArray(8);
            ba3 = ba1.And(ba2);   // 按位与，有一为假全为假 0000 1100: 12

            // ba3 的内容
            Console.WriteLine("Bit array ba3 after AND operation: 12");
            for (int i = 0; i < ba3.Count; i++)
            {
                Console.Write("{0, -6} ", ba3[i]);
            }
            Console.WriteLine();

            ba3 = ba1.Or(ba2);  // 按位或，有一为真全为真 0011 1101: 61
            // ba3 的内容
            Console.WriteLine("Bit array ba3 after OR operation: 61");
            for (int i = 0; i < ba3.Count; i++)
            {
                Console.Write("{0, -6} ", ba3[i]);
            }
            Console.WriteLine();
            
            Console.ReadKey();
        }
    }
}
/* Output
Bit array ba1: 60 
False False True True True True False False 
Bit array ba2: 13
True False True True False False False False 
Bit array ba3 after AND operation: 12
False False True True False False False False 
Bit array ba3 after OR operation: 61
True False True True False False False False 
*/
```

ps：

BitArray 类：https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.bitarray?view=net-8.0
