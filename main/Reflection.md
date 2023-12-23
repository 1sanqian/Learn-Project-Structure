# 一、 Reflection 反射

reflection 是用于获取有关加载的`程序集`以及其中定义类型的信息，例如类，接口，值类型（结构和枚举）

反射提供封装程序集、模块和类型的对象。

[程序集](https://learn.microsoft.com/en-us/dotnet/framework/app-domains/)包含模块，模块包含类型，类型包含成员。

可以使用反射动态创建类型的实例、将该类型绑定到现有对象或从现有对象获取类型，然后可以调用该类型的方法或访问其字段和属性

反射的典型用途包括：

*   使用[Assembly](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly)定义和加载程序集、加载程序集清单中列出的模块，以及从此程序集中查找类型并创建它的实例。

*   使用[Module](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.module)来发现信息，例如包含模块的程序集和模块中的类，还可以获取模块上定义的所有全局方法或其他特定的非全局方法。

*   使用[ConstructorInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.constructorinfo)可以发现构造函数的名称、参数、访问修饰符（例如`public`或`private`）和实现细节（例如`abstract`或）等信息。`virtual`使用[Type的](https://learn.microsoft.com/en-us/dotnet/api/system.type)[GetConstructors](https://learn.microsoft.com/en-us/dotnet/api/system.type.getconstructors)或[GetConstructor](https://learn.microsoft.com/en-us/dotnet/api/system.type.getconstructor)方法来调用特定的构造函数。[](https://learn.microsoft.com/en-us/dotnet/api/system.type)

*   使用[MethodInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo)可以发现方法的名称、返回类型、参数、访问修饰符（例如`public`或`private`）和实现细节（例如`abstract`或`virtual`）等信息。使用[类型的](https://learn.microsoft.com/en-us/dotnet/api/system.type)[GetMethods](https://learn.microsoft.com/en-us/dotnet/api/system.type.getmethods)或[GetMethod](https://learn.microsoft.com/en-us/dotnet/api/system.type.getmethod)方法来调用特定方法。[](https://learn.microsoft.com/en-us/dotnet/api/system.type)

*   使用[FieldInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.fieldinfo)可以发现字段的名称、访问修饰符（例如`public`或`private`）和实现详细信息（例如`static`）等信息，并获取或设置字段值。

*   使用[EventInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.eventinfo)可以发现事件的名称、事件处理程序数据类型、自定义属性、声明类型和反射类型等信息，并添加或删除事件处理程序。

*   使用[PropertyInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.propertyinfo)可以发现属性的名称、数据类型、声明类型、反射类型以及只读或可写状态等信息，并获取或设置属性值。

*   使用[ParameterInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.parameterinfo)可以发现参数的名称、数据类型、参数是输入参数还是输出参数以及参数在方法签名中的位置等信息。

*   当在应用程序域的仅反射上下文中工作时，使用[CustomAttributeData可以发现有关自定义属性的信息。](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.customattributedata)[CustomAttributeData](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.customattributedata)允许在不创建属性实例的情况下检查属性。[System.Reflection.Emit](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.emit)命名空间的类提供了一种特殊形式的反射，使开发人员能够在运行时构建类型。

使用 C# 反射，可以执行以下操作：

1.获取类型信息：可以使用反射 API 获取类型的信息，如类的名称、命名空间、基类、实现的接口、属性、方法等。

2.创建对象：通过反射，可以在运行时根据类型信息创建对象的实例，而无需在编译时确定类型。

3.调用方法和访问属性：可以使用反射调用对象的方法、访问属性和字段，甚至可以调用私有成员。

4.操作程序集：反射使您能够加载和浏览程序集（包括动态加载的程序集），并检查其中的类型和成员。

5.动态修改对象和类型：您可以使用反射在运行时修改对象的属性和字段的值，添加或移除属性、字段和方法等。

6.创建泛型类型实例：通过反射，可以动态地创建泛型类型的实例，以及调用泛型方法。

通过反射 API 查看特性（Attribute）信息

```
[AttributeUsage(AttributeTargets.Class)]
public class CustomAttribute : Attribute
{
   //CustomAttribute特性具有一个Name属性，用于存储特性的名称。
   public string Name { get; set; }

   public CustomAttribute(string name)
   {
      Name = name;
   }
}

[Custom("SampleClass")]
public class MyClass
{
   public void MyMethod()
   {
      Console.WriteLine("MyMethod is called.");
   }
}

public class Program
{
   static void Main(string[] args)
   {
      //typeof(MyClass)获取MyClass类型的Type对象
      Type type = typeof(MyClass);

      // 获取类的特性
      object[] attributes = type.GetCustomAttributes(true);

      //遍历这些特性
      foreach (var attribute in attributes)
      {
         //特性是CustomAttribute类型的，我们将打印出特性的名称。
         if (attribute is CustomAttribute customAttribute)
         {
            Console.WriteLine("Attribute Name: " + customAttribute.Name);
         }
      }

      Console.ReadLine();
   }
}
```




