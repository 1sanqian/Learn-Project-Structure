# 一、 Property 属性

属性 是类（class）、结构（structure）和接口（interface）的命名（named）成员，类或结构中的成员变量或方法称为 域（Field），属性是域的扩展，且可使用相同的语法来访问

属性不会确定存储位置，相反，它们具有可读写或计算它们值的 访问器。访问器用于定义属性或为属性赋值时执行的语句

#### 1. 访问器

属性的访问器包括获取或者设置属性的可执行语句，访问器声明可包含一个 get 访问器，set 访问器，或者两者同时包含，这两个访问器用于检索该属性的值以及对其赋值：

```
public class Person
{
    // 属性语法是字段的自然延伸，字段定义存储位置
    public string? FirstName {get; set;}

    // 编译器同时还能实现  get 和 set  访问器 的正文, 特点初始化对于只读属性最有用
    public string? LastName {get; set;} = string.Empty

    // 自定义存储
    public string? Hobit 
    {
        get { return _hobit; }
        set { _hobit = value; }
    }
    private string? _hobit

     // 属性是单个表达式，可为 getter 或 setter 使用 expression-bodied 成员
      public int? Ago
      {
          get => _ago;
          set => _ago = value;
      }
      private int? _ago;
}
```

get 和 set 被属性定义是读-写属性

**注意：set 访问器中的关键字 `value` 是一个参数；而 get 访问器必须返回一个值**

#### 2. 验证

可以在 set 访问器中编写代码，确保由某个属性表示的值始终有效（验证某个值是否一直有效）

```
public class Person
{
    public string? FirstName
    {
        get => _firstName;
        // 确保 FirstName 不得为空白或空白符， 空白则抛出异常警告引发 ArgumentException，throw 表达式作为属性资源库验证
        set => _firstName = (!string.IsNullOrWhiteSpace(value)) ? value : throw new ArgumentException("First name must not be blank");
    }
    private string? _firstName;
}
```

#### 3. 访问控制

除了具有公共访问器的读/写属性（这不是属性唯一有效的可访问性），可以创建只读属性，或者对 get/set 访问器提供不同的可访问性

```
public class Person 
{
    // 不能声明具有 “public 访问器”  的 private 属性，以下 FirstName并非是访问器 
    // 可以任意代码访问 FirstName 属性，但只能 从 Person 类中的其他代码对 FirstName 进行赋值
    public string? FirstName {get; private set;}
}
```

属性声明还可以声明为 protected、internal、protected internal 

#### 4. 只读
 
限定对属性的修改，以便只能在构造函数中设置属性

```
public class Person
{
    public Person(string firstName) => FirstName = firstName;

    public string FirstName { get; }
}
```

#### 5. Iniy-only

若要支持初始值设定项，可以将 set 访问器设置为 init 访问器，即调用[对象初始值设定项](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/object-and-collection-initializers)向属性分配值

```
public class Person
{
    public Person() { }

    // SetsRequiredMembers 是一个自定义的特性，可以强制使用该构造函数来创建 Person 对象，并要求在创建对象时必须提供 firstName 参数
    [SetsRequiredMembers]
    public Person(string firstName) => FirstName = firstName;
    
    // FirstName 添加了required 必要条件
    public required string FirstName { get; init; }
}

// 调用方必须将构造函数与 SetsRequiredMembers 一起使用，或者使用对象初始值设定项设置 FirstName 属性
var person = new VersionNinePoint2.Person("John");   
person = new VersionNinePoint2.Person{ FirstName = "John"};

/* Output
Error CS9035: Required member `Person.FirstName` must be set:
person = new VersionNinePoint2.Person();
*/
```

*不要将 required 与不可为 null 混淆。 将 required 属性设置为 null 或 default 是有效的。 如果类型不可为 null，例如这些示例中的 string，则编译器会发出警告*

#### 6. 计算属性 

属性无需只返回某个成员字段的值。 可以创建返回计算值的属性

```
public class Person
{
    public string? FirstName { get; set; }

    public string? LastName { get; set; }

    public string FullName { get { return $"{FirstName} {LastName}"; } }

    // 也可以使用 expression-bodied 成员，以更简洁的方式来创建 FullName 计算属性
    // public string FullName => $"{FirstName} {LastName}";
}
```

创建 “缓存的计算的属性”

```
// 1. 以便仅在第一次访问该属性时进行字符串格式设置,存在 bug，一旦 FirstName 或 LastName 改变，_fullName 就会无效
public class Person
{
    public string? FirstName { get; set; }

    public string? LastName { get; set; }

    private string? _fullName;
    public string FullName
    {
        get
        {
            if (_fullName is null)
                _fullName = $"{FirstName} {LastName}";
            return _fullName;
        }
    }
}

// 2. 更好的方法是在以上的基础上，对 FirstName 和 LastName 的set 方法进行修改
public class Person
{
    private string? _firstName;
    public string? FirstName
    {
        get => _firstName;
        set
        {
            _firstName = value;
            _fullName = null;
        }
    }

    private string? _lastName;
    public string? LastName
    {
        get => _lastName;
        set
        {
            _lastName = value;
            _fullName = null;
        }
    }

    private string? _fullName;
    public string FullName
    {
        get
        {
            if (_fullName is null)
                _fullName = $"{FirstName} {LastName}";
            return _fullName;
        }
    }
}
```

#### 7. 将特性附加到自动实现的属性

可在自动实现的属性中将字段特性附加到编译器生成的支持字段

 [NonSerializedAttribute](https://learn.microsoft.com/zh-cn/dotnet/api/system.nonserializedattribute) 只能附加到字段，不能附加到属性

可使用特性上的 `field:` 说明符将 [NonSerializedAttribute](https://learn.microsoft.com/zh-cn/dotnet/api/system.nonserializedattribute) 附加到 `Id` 属性的支持字段

```

public class Person
{
    public string? FirstName { get; set; }

    public string? LastName { get; set; }
    
    // 字段的特性NonSerializedAttribute可以借助 field: 说明符附加到 Id 属性 上
    [field:NonSerialized]
    public int Id { get; set; }

    public string FullName => $"{FirstName} {LastName}";
}
```

字段（Field）和属性（Property）是用于存储和访问数据的两种常见机制

+ 字段：字段可以直接通过字段名进行读取和写入，不需要使用方法或其他语法结构，通常用于存储对象的状态或数据，并且可以直接访问和修改
+ 属性：属性通过访问器方法进行读取和写入，可以提供更灵活的访问控制和逻辑，通常用于封装字段，并提供对字段的控制访问。属性可以添加逻辑，例如验证输入、计算值或触发事件

#### 8. [INotifyPropertyChanged](https://learn.microsoft.com/zh-cn/dotnet/api/system.componentmodel.inotifypropertychanged?view=net-8.0) 用于通知客户端（通常绑定客户端）属性值已更改

 INotifyPropertyChanged 接口用于通知数据绑定客户端值已更改。 当属性值发生更改时，该对象引发 INotifyPropertyChanged.PropertyChanged事件来指示更改， 数据绑定库则基于该更改来更新显示元素

```
public class Person : INotifyPropertyChanged
{
    public string? FirstName
    {
        get => _firstName;
        set
        {
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("First name must not be blank");
            if (value != _firstName)
            {
                _firstName = value;

                //?. 运算符称作 null 条件运算符。 它在计算运算符右侧之前会检查是否存在空引用
                PropertyChanged?.Invoke(this,
                    new PropertyChangedEventArgs(nameof(FirstName)));
            }
        }
    }
    private string? _firstName;

    public event PropertyChangedEventHandler? PropertyChanged;
}
```

+ Invoke(this, new PropertyChangedEventArgs(nameof(FirstName))) 用于触发 PropertyChanged 事件，并传递事件参数

+ this 表示当前对象（通常是实现了该事件的类的实例)

+ new PropertyChangedEventArgs(nameof(FirstName)) 创建一个新的 PropertyChangedEventArgs 对象，指定发生更改的属性名称（在此例中是 FirstName）

