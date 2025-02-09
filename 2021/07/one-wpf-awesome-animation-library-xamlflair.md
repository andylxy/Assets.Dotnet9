---
title: WPF牛逼的动画库：XamlFlair
slug: one-wpf-awesome-animation-library-xamlflair
description: XamlFlair库的目标是简化常见动画的实现，并允许开发人员使用几行Xaml`轻松`地添加单个或组合的动画集。
date: 2021-07-01 17:04:47
banner: true
copyright: Default
originaltitle: WPF牛逼的动画库：XamlFlair
draft: False
cover: https://img1.dotnet9.com/2021/07/cover_05.jpg
albums: 开源WPF
categories: WPF
tags: WPF,动画库,开源
---

>原文链接：https://github.com/XamlFlair/XamlFlair
>
>翻译：沙漠尽头的狼(本文未全文翻译，建议阅读原文了解更多)

![](https://img1.dotnet9.com/2021/07/0501.png)

# XamlFlair

XamlFlair库的目标是简化常见动画的实现，并允许开发人员使用几行Xaml`轻松`地添加单个或组合的动画集。

![Sample App](https://img1.dotnet9.com/2021/07/0502.gif)

# 展示

[Sekuence Puzzle Game](https://sekuence.fun)|
:-------------------------------------------:
![Sekuence](https://img1.dotnet9.com/2021/07/0503.gif)          |

## 支持作者

如果你想用一些咖啡来支持我的工作，你可以在这里做：[给我买杯咖啡](https://www.buymeacoffee.com/xamlflair)。你的帮助让我有动力继续花时间在这个项目上，并继续维护和更新它的新功能。提前谢谢！

## 内容

- [Install from Nuget](#install-from-nuget)

- [Features Overview](#features-overview)

- [Basic Concepts](#basic-concepts)

- [Usage](#usage)

- [Base Animation Types](#base-animation-types)

- [Color Animations (*WPF And Uno Only*)](#color-animations-wpf-and-uno-only)

- [Overriding the Global Default Values](#overriding-the-global-default-values)

- [Using a `ResourceDictionary` for Base Settings](#using-a-resourcedictionary-for-base-settings)

- [Default Animations (*WPF Only*)](#default-animations-wpf-only)

- [`TransformOn` Property (*WPF Only*)](#transformon-property-wpf-only)

- [Perspective Rotations (*UWP Only*)](#perspective-rotations-uwp-only)

- [Combining Animations](#combining-animations)

- [Overriding Values](#overriding-values)

- [Relative Translations on X and Y Axes](#relative-translations-on-x-and-y-axes)

- [Compound Animations](#compound-animations)

- [Repeating Animations](#repeating-animations)

- [Events and Bindings](#events-and-bindings)

- [Primary and Secondary Completion Commands](#primary-and-secondary-completion-commands)

- [Using the `StartWith` Property](#using-the-startwith-property)

- [Using the `AllowOpacityReset` Property (*WPF Only*)](#using-the-allowopacityreset-property-wpf-only)

- [Using the `ClipToBounds` Property (*UWP And Uno Only*)](#using-the-cliptobounds-property-uwp-and-uno-only)

- [Debugging Animations](#debugging-animations)

- [Logging Animations](#logging-animations)

- [`ListViewBase` (_UWP and Uno_) and `ListBox`-based (_WPF_) Animations](#listviewbase-uwp-and-uno-and-listbox-based-wpf-animations)

## Nuget中下载

| Platform | Package | NuGet |
| -------- | -------- | ------- |
| UWP | [XamlFlair.UWP][UWPNuGet] | [![UWPNuGetShield]][UWPNuGet] |
| WPF | [XamlFlair.WPF][WPFNuGet] | [![WPFNuGetShield]][WPFNuGet] |
| Uno | [XamlFlair.Uno][UNONuGet] | [![UNONuGetShield]][UNONuGet] |

使用以下命令从**Package Manager Console**下载**XamlFlair**:

UWP:

```shell
Install-Package XamlFlair.UWP
```

> 您的应用程序必须至少针对Windows 10版本1809（内部版本17763）

WPF:

```shell
Install-Package XamlFlair.WPF
```

Uno:

```shell
Install-Package XamlFlair.Uno
```

> 您的UWP应用程序必须至少针对Windows 10版本1809（构建18362）

## 功能概述（Features Overview）

Feature                               | **UWP**     | **WPF**        | **UWP (Uno)**      | **iOS (Uno)**      | **Android (Uno)**  | **Wasm (Uno) EXPERIMENTAL**
------------------------------------- | ----------- | -------------- | ------------------ | ------------------ | ------------------ | ----------------------------
*Animation System*                    | Composition | Storyboards    | Storyboards        | Storyboards        | Storyboards        | Storyboards
*Transform Type*                      |     N/A     | TransformGroup | CompositeTransform | CompositeTransform | CompositeTransform | CompositeTransform
DefaultAnimations.xaml                |      -      |      ✔         |         -          |         -          |          -         |              -
`TransformOn`                         |      -      |      ✔         |         -          |         -          |          -         |              -
Compound Animations                   |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
Relative Translations                 |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
Repeating Animations                  |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
Events & Bindings                     |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
Primary/Secondary Completion Commands |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
`StartWith`                           |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
`AllowOpacityReset`                   |      -      |      ✔         |         -          |         -          |          -         |              -
`ClipToBounds`                        |      ✔      |     N/A        |         ✔          |         ✔          |          ✔         |              ✔
Animated Lists                        |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
Blur Effect                           |      ✔      |      ✔         |         -          |         -          |          -         |              -
Saturation Effect                     |      ✔      |      -         |         -          |         -          |          -         |              -
Tint Effect                           |      ✔      |      -         |         -          |         -          |          -         |              -
Color Animations                      |      -      |      ✔         |         ✔          |         ✔          |          ✔         |              ✔
Perspective Rotations (Swivel)        |      ✔      |      -         |         -          |         -          |          -         |              -
Debugging Animations                  |      ✔      |      ✔         |         ✔          |         ✔          |          ✔         |              -

## 基本概念（Basic Concepts）

XamlFlair的基本概念是基于From和To的动画。由From动画组成的任何UI元素都将以**一个或多个任意值开始，并使用相应属性的默认值完成**。由To动画组成的任何UI元素都将**以其当前状态开始，并设置为一个或多个任意值**。

From动画的示例（一个移动到Translation(0)的UI元素）：

![From动画](https://img1.dotnet9.com/2021/07/0504.gif)

To动画示例（从当前状态滑出的UI元素）：

![To动画](https://img1.dotnet9.com/2021/07/0505.gif)

>**注意**：需要注意的是，对于彩色动画，此规则有一个例外，这在“基本动画类型”部分中进行了说明。


## 使用

首先，需要添加以下Xaml命名空间引用：

UWP and Uno:
```xml
xmlns:xf="using:XamlFlair"
```

WPF:
```xml
xmlns:xf="clr-namespace:XamlFlair;assembly=XamlFlair.WPF"
```

给任何需要动画的UI元素`FrameworkElement`添加附加属性：

```xml
<Border xf:Animations.Primary="{StaticResource FadeIn}" />
```

>**注意**：如果`FrameworkElement`在Xaml中定义了`CompositeTransform`，则它将在动画过程中更改。

>**注意**：`StaticResource`的用法是引用全局通用动画，这将在下一节中讨论。

### 基本动画类型（Base Animation Types）

#### 淡入淡出（Fade）

![淡入淡出动画](https://img1.dotnet9.com/2021/07/0506.gif)

>**警告**：设置`FadeTo`动画时要小心，因为如果`Visibility`是`Visible`，元素将保留在可视树中。在某些情况下，您可能需要手动管理`IsHitTestVisible`，以允许用户点击元素。

#### 移动（Translate）

![移动动画](https://img1.dotnet9.com/2021/07/0507.gif)

#### 缩放（Scale）

![缩放动画](https://img1.dotnet9.com/2021/07/0508.gif)

#### 旋转（Rotate）

![旋转动画](https://img1.dotnet9.com/2021/07/0509.gif)

#### 模糊 (Blur，只支持UWP 和 WPF)

![模糊动画](https://img1.dotnet9.com/2021/07/0510.gif)

#### 饱和度 (Saturate，只支持UWP)

![饱和度动画](https://img1.dotnet9.com/2021/07/0511.gif)

#### 色调（Tint）(只支持UWP)

![色调动画](https://img1.dotnet9.com/2021/07/0512.gif)

#### 色彩 (Color，只支持WPF和Uno)

![色彩动画](https://img1.dotnet9.com/2021/07/0513.gif)

>**注意**：重要的是要注意，当使用`From`动画设置色彩动画时，颜色将从指定值设置为其**当前状态**，而不是默认值。

#### 旋轴 (Swivel，只支持UWP)

![旋轴动画](https://img1.dotnet9.com/2021/07/0514.gif)

>**注意**：请阅读[Perspective Rotations (*UWP Only*)](#perspective-rotations-uwp-only)一节了解更多详细信息。

下面列出了使用XamlFlair时一些值得注意的`默认值`：

- **Kind**: FadeTo
- **Duration** (_milliseconds_): 500
- **Easing**: Cubic
- **Easing Mode**: EaseOut
- **TransformCenterPoint**: (0.5, 0.5)
- **Event**: Loaded
- **InterElementDelay** (_milliseconds_): 25 (_List controls only_)
- **TransformOn**: Render (_WPF only_)
- **Saturation**: 0.5 (_UWP only_)
- **Tint**: Transparent (_UWP only_)

### 色彩动画 (Color Animations，只支持WPF和Uno)

使用色彩动画时需要注意，因为它们与其他基本类型动画**略有**不同。使用`ColorTo`和`ColorFrom`时，必须执行以下操作：

- 只能设置以下属性的动画： `Control.Background`, `Control.Foreground`, `Control.BorderBrush`, `Border.Background`, `Border.BorderBrush`, `TextBlock.Foreground`, `Shape.Fill`, `Shape.Stroke`
- 确保在要设置动画的相应属性上设置brush
- 还必须使用`ColorOn`指定目标属性

以下示例将为Rectangle的`Fill`属性设置从RoyalBlue到DarkGreen的动画：

```html
<xf:AnimationSettings x:Key="SampleColorAnimation"
                      Kind="ColorTo"
                      Color="DarkGreen"
                      ColorOn="Fill" />

<Rectangle Fill="RoyalBlue"
           xf:Animations.Primary="{StaticResource SampleColorAnimation}" />
```

### 覆盖全局默认值

如果需要全局更改默认动画值之一（例如，默认`Duration`为750而不是500），则可以在应用程序的初始化代码中调用`OverrideDefaultSettings`函数。以下示例更改`Duration`和`Easing`的默认值：

```cs
XamlFlair.Animations.OverrideDefaultSettings(
    duration: 750,
    easing: EasingType.Quadratic);
```

因此，在上面的示例代码中，每个动画都将以二次缓和的方式运行750ms。

### 使用`ResourceDictionary`进行基本设置

所有`常见`动画都应该放在全局`ResourceDictionary`（例如：`Animations.xaml`）中，并在应用程序中需要时使用。目标是将所有动画合并为一个具有有意义名称的文件，以便任何开发人员都能准确地了解将动画应用到`FrameworkElement`中的内容。下面是一个小例子：

```html
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:xf="using:XamlFlair">

    <x:Double x:Key="PositiveOffset">50</x:Double>
    <x:Double x:Key="NegativeOffset">-50</x:Double>
    <x:Double x:Key="SmallScaleFactor">0.75</x:Double>
    <x:Double x:Key="LargeScaleFactor">1.25</x:Double>

    <xf:AnimationSettings x:Key="FadeIn"
                          Kind="FadeFrom"
                          Opacity="0" />

    <xf:AnimationSettings x:Key="FadeOut"
                          Kind="FadeTo"
                          Opacity="0" />

    <!-- Scale to a larger value -->
    <xf:AnimationSettings x:Key="Expand"
                          Kind="ScaleXTo,ScaleYTo"
                          ScaleX="{StaticResource LargeScaleFactor}"
                          ScaleY="{StaticResource LargeScaleFactor}" />

    <!-- Scale from a larger value -->
    <xf:AnimationSettings x:Key="Contract"
                          Kind="ScaleXFrom,ScaleYFrom"
                          ScaleX="{StaticResource LargeScaleFactor}"
                          ScaleY="{StaticResource LargeScaleFactor}" />
.
.
.
</ResourceDictionary>
```

要设置应用程序中已有的这组预配置`AnimationSettings`，请执行以下步骤：

1. 项目工程点击右键菜单，点击**Add > New Item...**
2. 选择 **Resource Dictionary** 并命名为 `Animations.xaml`
3. `App.xaml`内容如下:

```html
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="Animations.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

4. 在 `Animations.xaml`中,复制粘贴以下相应链接中的内容

- [Animation settings for UWP](https://github.com/XamlFlair/XamlFlair/blob/master/Samples/XamlFlair.Samples.UWP/Animations.xaml)

- [Animation settings for WPF](https://github.com/XamlFlair/XamlFlair/blob/master/Samples/XamlFlair.Samples.WPF/Animations.xaml)

- [Animation settings for Uno](https://github.com/XamlFlair/XamlFlair/blob/master/Samples/Uno/XamlFlair.Samples.Uno.Shared/Animations.xaml)

你的应用程序现在有一组`通用`动画可以使用了。

### 默认动画 (*只支持WPF*)

除了创建包含自定义`AnimationSettings`的`ResourceDictionary`之外，XamlFlair还提供一些`默认`动画。

要在应用程序中引用这些默认动画，请在`App.xaml`中执行以下步骤：

1. 顶部添加`XamlFlair.WPF`命名空间

```xml
    xmlns:xf="clr-namespace:XamlFlair;assembly=XamlFlair.WPF"
```

2. 更新应用程序资源（Application Resources）:

```html
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <xf:XamlFlairResources />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

您的应用程序现在有一系列全局`默认`的动画可以使用了。

如果Visual Studio Intellisense在使用`<xf:XamlFlairResources />`时不起作用，您可能需要尝试以下操作：

```html
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="pack://application:,,,/XamlFlair.WPF;component/DefaultAnimations.xaml"/>
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

### `TransformOn` 属性 (*只支持WPF*)

`RenderTransform`可使用`TransformOn`属性应用动画。可用选项为`Render`和`Layout`。未指定任何内容时，默认为`Render`。以下是关于两个选项的示例：

![TransformOn动画](https://img1.dotnet9.com/2021/07/0515.gif)

>**注意**：非常重要的是要注意WPF的`LayoutTransform`不支持任何`TranslateTransform`，因此translate动画永远不会生效。您可以在[这里](https://docs.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement.layouttransform?redirectedfrom=MSDN&view=net-5.0#remarks)的备注部分了解更多信息。

原文readme.md太长了，翻译累了，大家有兴趣看原文吧，最后上一图：

![](https://img1.dotnet9.com/2021/07/0516.gif)

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/07/2021-07-01_01.md)