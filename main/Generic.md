# Generic 泛型

Generic 泛型允许在编写类、结构、接口和方法时使用类型参数，以便在实际使用时指定具体的类型

##### 泛型类示例
```
public class MyGenericClass<T>
{
    private T genericField;

    public MyGenericClass(T value)
    {
        genericField = value;
    }

    public T GenericMethod(T param)
    {
        // 执行针对 T 类型的操作
        return param;
    }
}
```

##### 泛型方法示例

```
public class MyGenericMethods
{
    public T GenericMethod<T>(T value)
    {
        // 执行针对 T 类型的操作
        return value;
    }
}
```

##### 泛型委托示例

```
delegate T NumberChanger<T>(T n);
namespace GenericDelegateAppl
{
    class TestDelegate
    {
        static int num = 10;
        public static int AddNum(int p)
        {
            num += p;
            return num;
        }

        public static int MultNum(int q)
        {
            num *= q;
            return num;
        }
        public static int getNum()
        {
            return num;
        }

        static void Main(string[] args)
        {
            // 创建委托实例
            NumberChanger<int> nc1 = new NumberChanger<int>(AddNum);
            NumberChanger<int> nc2 = new NumberChanger<int>(MultNum);
            // 使用委托对象调用方法
            nc1(25);
            Console.WriteLine("Value of Num: {0}", getNum());
            nc2(5);
            Console.WriteLine("Value of Num: {0}", getNum());
            Console.ReadKey();
        }
    }
}

/* Output
Value of Num: 35
Value of Num: 175
*/
```

##### 声明泛型时限定条件

语法

```
public class MyClass<T> where T : U
{
    // 泛型类或方法的定义
}
```

指定基类：

```
public class MyClass<T> where T : MyBaseClass
{
    // T 必须是 MyBaseClass 类型或其子类
}
```

指定接口：

```
public class MyClass<T> where T : IMyInterface
{
    // T 必须实现 IMyInterface 接口
}
```

多重约束：

```
public class MyClass<T, U> where T : MyBaseClass, IMyInterface where U : struct
{
    // T 必须是 MyBaseClass 类型或其子类，并且实现 IMyInterface 接口
    // U 必须是值类型
}
```
##### 泛型的作用

类型安全： 泛型提供了编译时类型检查，避免了运行时的类型转换错误
代码复用： 可以编写更通用和灵活的代码，不需要为每种类型编写重复的逻辑，可以创建自己的泛型接口、泛型类、泛型方法、泛型事件和泛型委托
性能优化： 泛型避免了装箱和拆箱操作，提高了代码的执行效率

ps:

泛型： https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic?view=net-8.0

