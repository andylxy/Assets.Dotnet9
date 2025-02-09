---
title: Blazor创建TabControl组件
slug: blazor-creates-tabcontrol-component
description: 创建一个Blazor TabControl组件，有两个目标知识点
date: 2021-12-06 22:01:36
copyright: Reprint
author: Blazor University
originaltitle: Blazor创建TabControl组件
originallink: https://blazor-university.com/templating-components-with-renderfragements/creating-a-tabcontrol/
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_03.jpeg
categories: Blazor
tags: 前端,Blazor
---

>不是全文翻译，部分翻译自认为可能不准确，就原文照搬了。

创建一个Blazor TabControl组件，有两个目标知识点：

1. Pass data into a RenderFragment to give it context.
2. Use a CascadingParameter to pass the parent TabControl component into its child TabPage components.

下面看最终效果图：

![](https://img1.dotnet9.com/2021/12/0301.gif)

## 实操开始：

请先创建一个Blazor项目（Blazor Client或者Server皆可，我们以Blazor Server为例），

第一步，创建两个组件：`TabControl`和`TabPage`。`TabPage`组件有一个父`TabControl`属性引用（属性名`Parent`，添加`CascadingParameter`特性）。

**TabControl组件：**

文件路径：./Shared/TabControl.razor

```html
<div>这是一个TabControl</div>
<CascadingValue Value="this">
    @ChildContent
</CascadingValue>

@code {
    // 如果我们想以<TabPage>标签的形式使用TabPage,那么下面的代码是必须的
    [Parameter]
    public RenderFragment? ChildContent { get; set; }
}
```

**TabPage组件：**

文件路径：./Shared/TabPage.razor

```html
<div>这是一个TabPage</div>
@ChildContent

@code {
    [CascadingParameter]
    private TabControl? Parent { get; set; }

    [Parameter]
    public RenderFragment? ChildContent { get; set; }

    protected override void OnInitialized()
    {
        if (Parent == null)
            throw new ArgumentNullException(nameof(Parent), "TabPage必须包含TabControl引用");
        base.OnInitialized();
    }
}
```

## TabControl关联TabPage

在`TabPage`的`OnInitialized`方法中添加下面这一行代码，使`TabPage`关联上`TabControl`：

```C#
Parent.AddPage(this);
```

`AddPage`方法见下面的代码，在`TabControl`调用`AddPage`方法保存引用后，我们在`TabControl`中添加`ActivePage`属性，同样看下面的代码：

```C#
public TabPage? ActivePage { get; set; }
readonly List<TabPage> _pages = new();

internal void AddPage(TabPage tabPage)
{
    _pages.Add(tabPage);
    if (_pages.Count == 1)
        ActivePage = tabPage;
    StateHasChanged();
}
```

给`AddPage`组件添加一个`Text`属性用于展示。

```C#
[Parameter]
public string? Text { get; set; }
```

在`TabControl`中添加以下标签（在`ChildContent`渲染之前），这些标签会一次性全部渲染出来，当点击某个`TabPage`时会改变`TabControl`的选择项。

```html
<div class="btn-group" role="group">
  @foreach (TabPage tabPage in Pages)
  {
    <button type="button"
      class="btn @GetButtonClass(tabPage)"
      @onclick=@( ()=>ActivatePage(tabPage) )>
      @tabPage.Text
    </button>
  }
</div>
```

上面这些标签会创建标准的Bootstrap按钮组，每个`TabPage`会创建一个有以下特征的按钮：

1. CSS类设置为"btn"，并通过`GetButtonClass`方法追加CSS类名，如果当前`TabPage`为`ActivePage`，添加CSS类`btn-primary`，否则添加`btn-secondary`。
2. 当点击按钮时会激活点击的`TabPage`。

**注意**：`@onclick`需要关联一个无参的方法，所以lambda表达式用一个内联的`@( )`来设置点击的`TabPage`为`ActivatePage`。

3. 按钮的文字通过`TabPage`的`Text`属性设置。

下面的代码添加到`TabControl`的代码区域。

```C#
string GetButtonClass(TabPage page)
{
  return page == ActivePage ? "btn-primary" : "btn-secondary";
}
void ActivatePage(TabPage page)
{
  ActivePage = page;
}
```

## 使用`TabControl`

添加一个`TabControlTest`组件：

文件名：./Pages/TabControlTest.razor

```html
@page "/tabcontroltest"

<TabControl>
    <TabPage Text="Tab 1">
        <h1>The first tab</h1>
    </TabPage>
    <TabPage Text="Tab 2">
        <h1>The second tab</h1>
    </TabPage>
    <TabPage Text="Tab 3">
        <h1>The third tab</h1>
    </TabPage>
</TabControl>

@code {

}
```

在./Shared/NavMenu中添加`TabControlTest`路由

```html
省略部分代码
<div class="nav-item px-3">
    <NavLink class="nav-link" href="tabcontroltest">
        <span class="oi oi-plus" aria-hidden="true"></span> TabControl Test
    </NavLink>
</div>
省略部分代码
```

这样就完了吗？我们看看现在的效果：

![](https://img1.dotnet9.com/2021/12/0302.png)

不对吧，三个`TabPage`的内容全部显示出来了，解决这个问题只需要在`TabPage`渲染`ChildContent`时判断当前`TabPage`是否为`TabControl`选中的页，选中项才进行渲染：

```C#
@if (Parent.ActivePage == this)
{
  @ChildContent
}
```

OK代码完，效果见本文开头。

>文中代码已放:[Github](https://github.com/dotnet9/BlazorDemo)

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-06_02.md)