---
title: 你知道WPF与WinForms的区别吗？
slug: do-you-know-the-difference-between-wpf-and-winform
description: 介绍两种开发Windows桌面应用程序的方法之间的主要区别，这些方法可以在现代系统开发中发挥更好的作用。
date: 2021-04-07 23:59:02
copyright: Default
originaltitle: 你知道WPF与WinForms的区别吗？
draft: False
cover: https://img1.dotnet9.com/2021/04/cover_06.jpg
categories: .NET相关,Winform,WPF
tags: .NET,Winform,WPF
---

## 介绍
 
WPF的缩写指微软的Windows Presentation Foundation，而WinForms是Windows Forms Applications的简单组合。这两个都是微软的Windows应用程序图形用户界面，开发人员可以使用它们来开发Windows桌面应用程序。本文重点介绍两种开发Windows桌面应用程序的方法之间的主要区别，这些方法可以在现代系统开发中发挥更好的作用。
 
## Windows Forms
 
WinForms于2002年2月作为.Net Framework的一部分引入。在很大程度上，WinForms允许开发人员在Windows窗体上拖放控件，并允许开发人员使用可以具有C＃，VB.NET或任何其他.NET语言的代码隐藏文件来操纵这些控件。每个WinForms控件都是一个类的实例，因为WinForms作为具有一组C++类的包装器存在。Microsoft的Visual Studio使WinForms的开发更容易，因为开发人员可以轻松地从工具箱中拖放控件。

WinForms工具箱中的控件：

![WinForms工具箱中的控件](https://img1.dotnet9.com/2021/04/0601.jpg)

在WinForms桌面应用程序中，开发人员只能访问他们可以在其中操纵控件事件的代码隐藏文件。WinForms桌面应用程序在控件的功能和应用程序行为方面有其局限性，这将在下一部分中揭示。
 
## WPF桌面应用程序
 
与WinForms不同，WPF的体系结构包含三个主要组件：a presentation framework, presentation core, and mallcore。WPF并不完全依赖于标准Windows控件，因此是一种独立方式。2007年，Microsoft引入了Windows Presentation Foundation（WPF），以交替WinForms来进行.Net Framework桌面应用程序开发。这一交替带来了桌面应用程序开发中的许多变化。首先，WPF将设计人员和程序员分开，可以使用Visual Studio或Blend分别设计UI，而开发人员可以使用代码隐藏文件来操纵控件事件。
 
WPF使用XAML创建控件，其文件结构更像ASP.NET，您可以自由使用设计器或编写XAML代码来创建控件。使用Canvas Panel的设计师仍然可以像在WinForms中一样在Windows页面上拖放控件。WPF带来的主要区别是XAML文件和对XAML文件附带的可见设计器的访问。

WPF可视化设计和XAML文件编辑：

![WPF可视化设计和XAML文件编辑](https://img1.dotnet9.com/2021/04/0602.jpg)

上图显示了WPF应用程序的布局，其中在Designer旁边显示了XAML文件。
 
WPF项目的文件结构如下：

![WPF项目的文件结构](https://img1.dotnet9.com/2021/04/0603.jpg)

- 每个窗口或页面都有一个用于添加控件的.xaml文件以及一个.cs，.vb等文件，后者是代码隐藏文件，更像是ASP.NET方式。
- 与WinForms不同，WPF生成一个初始MainWindow来启动应用程序，并且要更改启动窗口，可以在App.xaml文件中执行此操作。

WPF主窗体启动配置：

![WPF主窗体启动配置](https://img1.dotnet9.com/2021/04/0604.jpg)

- 该文件充当应用程序的条目。

WPF与WinForms的其他显著区别是控件。要添加控件，您只需要编写简单的XAML代码。例如，要在WPF窗口中添加文本框，你可以写如下代码实现：

```html
<Window x:Class="WpfApp1.MainWindow"  
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"  
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"  
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"  
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"  
    xmlns:local="clr-namespace:WpfApp1"  
   mc:Ignorable="d"  
   Title="MainWindow" Height="450" Width="800">  
    <StackPanel>  
        <TextBox></TextBox>  
    </StackPanel>  
</Window>
```

请注意语法中的标记，该标记建议使用名称“扩展应用程序标记语言（XAML）”。XAML代码放置在Window标记中。控件标签可能具有描述控件宽度，高度等的属性，具体取决于控件。
 
WPF还带来了与WinForms的另一个显著区别，那就是可以添加带有图像的Button的功能。在WinForms中，向按钮添加图像意味着必须自己绘制图像或包含一些第三方控件，但是WPF按钮控件很简单，您可以向其中添加任何内容。

```html
<Window x:Class="WpfApp1.MainWindow"  
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"  
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"  
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"  
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"  
    xmlns:local="clr-namespace:WpfApp1"  
   mc:Ignorable="d"  
   Title="MainWindow" Height="500" Width="800">  
    <Button Padding="5">  
        <StackPanel Orientation="Horizontal">  
            <Image Source="/Image.jpg" Height="25" Width="50" />  
            <TextBlock Margin="5,0">I'm a Button</TextBlock>  
        </StackPanel>  
    </Button>  
</Window>  
```

输出如下所示：WPF运行演示

![WPF运行演示](https://img1.dotnet9.com/2021/04/0605.jpg)

WPF还提供了完全受支持的数据绑定功能，如下面的示例所示：

```html
<Window x:Class="WpfApp1.MainWindow"  
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"  
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"  
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"  
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"  
    xmlns:local="clr-namespace:WpfApp1"  
   mc:Ignorable="d"  
        Title="MainWindow" Height="500" Width="800">  
    <StackPanel Margin="10">  
        <WrapPanel Margin="0,10">  
            <Label Content="Your Text Here:" FontWeight="Bold"/>  
            <TextBox Name="txtBind" Height="20" Width="250"  RenderTransformOrigin="-2.75,0.587" Margin="59,0,336,0"/>  
        </WrapPanel>  
        <WrapPanel Margin="0,10">  
            <TextBlock Text="Bound-Text: " FontWeight="Bold" />  
            <TextBlock Text="{Binding Path=Text, ElementName=txtBind}" />  
        </WrapPanel>  
    </StackPanel>  
</Window>  
```

输出：WPF数据绑定演示

![WPF数据绑定演示](https://img1.dotnet9.com/2021/04/0606.jpg)

上例中的{Binding}属性用于将&lt;TextBlock&gt;中的文本绑定到txtBindTextBox中的文本。这只是说明使用{Binding}属性在WPF中绑定数据有多么简单。
 
## 结论
 
本文通过两种创建桌面应用程序的.NET方式之间的体系结构，语法，文件结构以及应用程序行为差异，展示了WinForms和WPF之间的主要差异。尽管WinForms设计看似友好和直接，但是XAML带来了开发人员在现代桌面应用程序中可能需要的一些有用功能。
  
>原文链接：https://www.c-sharpcorner.com/article/wpf-vs-winforms/

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/04/2021-04-07_01.md)