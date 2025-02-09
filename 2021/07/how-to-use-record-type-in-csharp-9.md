---
title: 如何在 C# 9 中使用record类型？
slug: how-to-use-record-type-in-csharp-9
description: 使用过record吗？
date: 2021-07-10 22:27:47
copyright: Default
originaltitle: 如何在 C# 9 中使用record类型？
draft: False
cover: https://img1.dotnet9.com/2021/07/cover_03.jpg
categories: .NET相关
tags: C#,Record
---

>原文链接：[https://www.infoworld.com/article/3607372/how-to-work-with-record-types-in-csharp-9.html](https://www.infoworld.com/article/3607372/how-to-work-with-record-types-in-csharp-9.html)
>
>原文标题：[How to work with record types in C# 9](https://www.infoworld.com/article/3607372/how-to-work-with-record-types-in-csharp-9.html)
>
>翻译：沙漠尽头的狼(谷歌翻译加持)

利用 C# 9 中的`record`类型来构建不可变类型和线程安全对象。

不可变性使您的对象线程安全并有助于改进内存管理。它还使您的代码更具可读性和更易于维护。不可变对象被定义为一旦创建就无法更改的对象。因此，不可变对象本质上是线程安全的，并且不受竞争条件的影响。

直到最近，C# 还不支持开箱即用的不可变性。C# 9 通过新的 `init-only` 属性和`record`类型引入了对不可变性的支持。仅`init-only`属性可用于使对象的各个属性不可变，而`record`可用于使整个对象不可变。

因为不可变对象不会改变它们的状态，所以在多线程和数据传输对象等许多用例中，不可变性是一个理想的特性。本文讨论了我们如何在 C# 9 中使用 `init-only` 属性和`record`类型。

要使用本文中提供的代码示例，您应该在系统中安装 Visual Studio 2019。如果您还没有安装，可以在此处下载 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。

## 在 Visual Studio 中创建控制台应用程序项目

首先，让我们在 Visual Studio 中创建一个 .NET Core 控制台应用程序项目。假设您的系统中安装了 Visual Studio 2019，请按照下面概述的步骤在 Visual Studio 中创建一个新的 .NET Core 控制台应用程序项目。

1. 启动 Visual Studio IDE。
2. 单击“Create new project.”。
3. 在“Create new project”窗口中，从显示的模板列表中选择“Console App (.NET Core)”。
4. 点击下一步。
5. 在接下来显示的“Configure your new project”窗口中，指定新项目的名称和位置。
6. 单击创建。

遵循这些步骤将在 Visual Studio 2019 中创建一个新的 .NET Core 控制台应用程序项目。我们将在本文的后续部分中使用该项目。

## 在 C# 9 中使用 init-only 属性

`init-only`属性是那些只能在对象初始化时赋值的属性。请参阅以下包含 init-only 属性的类。

```C#
public class DbMetadata
{
    public string DbName { get; init; }
    public string DbType { get; init; }
}
```

您可以使用以下代码片段创建 DbMetadata 类的实例并初始化其属性。

```C#
DbMetadata dbMetadata = new DbMetadata()
{
      DbName = "Test",
      DbType = "Oracle"
};
```

请注意，对 `init-only` 字段的后续分配是非法的。因此，以下语句将无法编译。

```C#
dbMetadata.DbType = "SQL Server";
```

## 在 C# 9 中使用record类型

C# 9 中的`record`类型是仅具有只读属性的轻量级、不可变数据类型（或轻量级类）。因为`record`类型是不可变的，所以它是线程安全的，并且在创建后不能改变或更改。您只能在构造函数中初始化`record`类型。

您可以使用 `record` 关键字声明`record`，如下面的代码片段所示。

```C#
public record Person
{
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string Address { get; set; }
  public string City { get; set; }
  public string Country { get; set; }
}
```

请注意，仅将类型标记为`record`（如前面的代码片段所示）本身不会为您提供不可变性。要为您的`record`类型提供不可变性，您必须使用 init 属性，如下面的代码片段所示。

```C#
public record Person
{
  public string FirstName { get; init; }
  public string LastName { get; init; }
  public string Address { get; init; }
  public string City { get; init; }
  public string Country { get; init; }
}
```

您可以使用以下代码片段创建 Person 类的实例并初始化其属性。

```C#
var person = new Person
{
  FirstName = "Joydip",
  LastName = "Kanjilal",
  Address = "192/79 Stafford Hills",
  City = "Hyderabad",
  Country = "India"
};
```

## 在 C# 9 中使用 with 表达式

如果某些属性具有相同的值，您可能经常希望从另一个对象创建一个对象。但是，记录类型的 `init-only` 属性会阻止这种情况。例如，以下代码片段将无法编译，因为默认情况下名为 Person 的`record`类型的所有属性都是 `init-only`。

```C#
var newPerson = person;
newPerson.Address = "112 Stafford Hills";
newPerson.City = "Bangalore";
```
  
幸运的是，有一个解决方法——with 关键字。通过指定属性值的更改，您可以利用 with 关键字从另一个`record`类型创建一个实例。以下代码片段说明了如何实现这一点。

```C#
var newPerson = person with
            { Address = "112 Stafford Hills", City = "Bangalore" };
```
                   
## C# 9 中record类型的继承

`record`类型支持继承。也就是说，您可以从现有`record`类型创建新`record`类型并添加新属性。以下代码片段说明了如何通过扩展现有`record`类型来创建新`record`类型。

```C#
public record Employee : Person
{
   public int Id { get; init; }
   public double Salary { get; init; }
}
```
        
## C# 9 中的位置record

默认情况下，使用位置参数创建的`record`类型实例是不可变的。换句话说，您可以通过使用构造函数参数传递有序的参数列表来创建`record`类型的不可变实例，如下面给出的代码片段所示。

```C#
var person = new Person("Joydip", "Kanjilal", "192/79 Stafford Hills", "Hyderabad", "India");
```

## 在 C# 9 中检查record实例是否相等

在 C# 中检查类的两个实例是否相等时，比较基于这些对象的引用（身份）。但是，如果您检查`record`类型的两个实例是否相等，则比较基于`record`类型的实例中的值。

以下代码片段说明了一个名为 DbMetadata 的`record`类型，它由两个字符串属性组成。

```C#
public record DbMetadata
{
   public string DbName { get; init; }
   public string DbType { get; init; }
}
```     
        
以下代码片段显示了如何创建 DbMetadata 记录类型的两个实例。

```C#
DbMetadata dbMetadata1 = new DbMetadata()
{
   DbName = "Test",
   DbType = "Oracle"
};
DbMetadata dbMetadata2 = new DbMetadata()
{
   DbName = "Test",
   DbType = "SQL Server"
};
```
  
您可以使用 `Equals` 方法检查相等性。以下两个语句将在控制台窗口中显示“false”。

```C#
Console.WriteLine(dbMetadata1.Equals(dbMetadata2));
Console.WriteLine(dbMetadata2.Equals(dbMetadata1));
```

考虑以下创建 DbMetadata `record`类型的第三个实例的代码片段。请注意，实例 dbMetadata1 和 dbMetadata3 包含相同的值。

```C#
DbMetadata dbMetadata3 = new DbMetadata()
{
   DbName = "Test",
   DbType = "Oracle"
};
```  
     
以下两条语句将在控制台窗口中显示“true”。

```C#
Console.WriteLine(dbMetadata1.Equals(dbMetadata3));
Console.WriteLine(dbMetadata3.Equals(dbMetadata1));
```

尽管`record`类型是引用类型，但 C# 9 提供了合成方法来遵循基于值的相等语义。编译器为您的`record`类型生成以下方法以强制实施基于值的语义：

- `Object.Equals(Object)` 方法的重载
- 接受`record`类型作为其参数的虚拟 `Equals` 方法
- `Object.GetHashCode()` 方法的重载
- 两个相等运算符的方法，即 `==` 运算符 和 `!=` 运算符 
- `record`类型实现 `System.IEquatable<T>`

此外，记录类型提供了 `Object.ToString()` 方法的重载。这些方法是隐式生成的，您无需重新实现它们。

## 检查 C# 中的 Equals 方法

您可以检查是否已隐式生成了 `Equals` 方法。为此，请在 DbMetadata 记录中添加一个 Equals 方法，如下所示。

```C#
public record DbMetadata
{
    public string DbName { get; init; }
    public string DbType { get; init; }
    public override bool Equals(object obj) =>
    obj is DbMetadata dbMetadata && Equals(dbMetadata);
}
```
    
当您编译代码时，编译器将用以下消息标记错误：

>Type 'DbMetadata' already defines a member called 'Equals' with the same parameter types

尽管`record`类型是一个类，但 `record` 关键字提供了额外的类似值类型的行为和语义，使`record`与类不同。`record`本身是一种引用类型，但它使用自己的内置相等性检查——相等性是通过值而不是引用来检查的。最后，请注意`record`可以是可变的，但它们主要是为不变性而设计的。

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/07/2021-07-10_02.md)