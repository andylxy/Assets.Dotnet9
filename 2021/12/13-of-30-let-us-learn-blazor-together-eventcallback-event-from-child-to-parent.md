---
title: (13/30)大家一起学Blazor：EventCallback, event from child to parent
slug: 13-of-30-let-us-learn-blazor-together-eventcallback-event-from-child-to-parent
description: 目前的4篇日志是来自我们写好的假数据，但正常来说不会这样做，而是有个按钮让用户点击了之后，增加或减少日志的数量。
date: 2021-12-16 20:48:19
copyright: Reprint
author: StrayaWorker
originaltitle: (13/30)大家一起学Blazor：EventCallback, event from child to parent
originallink: https://ithelp.ithome.com.tw/articles/10262982
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums: 学Blazor
categories: Blazor
tags: Blazor Server
---

目前的4篇日志是来自我们写好的假数据，但正常来说不会这样做，而是有个按钮让用户点击了之后，增加或减少日志的数量。

增加的按钮会放在`<Blog>`，点击了「增加」按钮产生一条新的Post供用户输入，再让用户点击「确认」按钮储存日志。

删除的按钮则可以放在`<Blog>`，再在`<Post>`加入checkbox，让用户自己勾选要删除哪些Post；或是放在`<Post>`，点击删除按钮就删除该条日志。

新增按钮很简单，只要在`<MyButton>`加上`@onclick`事件即可，开始之前，先将版面稍作修改，顺便把`FontSizeStyle`移除。

**Blog.razor**

```html
@page "/Blog"
@inherits BlogBase

@if (Blog == null)
{
    <p>Loading...</p>
}
else
{
    <div class="container">
        <div class="row">
            <div class="col pl-0">
                <label>博客名称</label>
                <input @bind-value="Blog.Name" class="form-control w-25"/>
                <MyButton value="Add" class="btn btn-info my-2" type="button" @onclick="Add"/>
            </div>
        </div>
        <div class="row">
            @if (Blog.Posts != null)
            {
                foreach (var post in Blog.Posts)
                {
                    <div class="col-md-3 border rounded p-3 mr-2 mb-2 w-25">
                        <CascadingValue Value="ColorStyle" Name="ColorStyle" IsFixed="true">
                            <Post Post="post" />
                        </CascadingValue>
                    </div>
                }
            }
        </div>
    </div>
}
```

C#部分则加入`Add()`方法，原来`LoadData()`的日志删除。

**BlogBase.razor.cs**

```C#
using BlazorServer.Models;
using Microsoft.AspNetCore.Components;

namespace BlazorServer.Pages;

public class BlogBase : ComponentBase
{
	public BlogModel? Blog { get; set; }

	public string? ColorStyle { get; set; } = "color: goldenrod";
	public string? FontSizeStyle { get; set; } = "color: goldenrod";

	protected override Task OnInitializedAsync()
	{
		LoadData();
		return base.OnInitializedAsync();
	}

	private void LoadData()
	{
		Blog = new BlogModel
		{
			Id = 1,
			Name = "我的博客",
			Posts = new List<PostModel>(),
			CreateDateTime = new DateTime(2021, 12, 14, 23, 46, 59)
		};
	}

	protected void Add()
	{
		Blog?.Posts?.Add(new PostModel());
	}
}
```

接着点击Add按钮，就可以看到日志条数增加了。

![](https://img1.dotnet9.com/2021/12/2101.gif)

接着来做Delete功能，在`Post.razor`加入Delete按钮。

![](https://img1.dotnet9.com/2021/12/2102.png)

但问题来了，当我点击Delete按钮，`<Blog>`怎么知道我删除的是哪一条Post？这时候就需要`Id`可以识别，于是加入一个私有变量`_postId`，每次点击`Add()`都+1，正常来说PostId会跟着Post而不是由Blog产生，不过因为还没接触到数据库，所以先这样将就，后面连接数据库后就会改变。

![](https://img1.dotnet9.com/2021/12/2103.png)

为了验证是否正确，删除原来`Post.razor`注释的Post.Id，加入新样式的Post.Id，可以看到没有问题。

![](https://img1.dotnet9.com/2021/12/2104.png)

现在有了识别Id，又产生了新问题，要怎么让`<Blog>`收到`Id`？目前Id由`<Blog>`产生，所以没这问题，等后面Id由数据库产生后，`<Blog>`就不会知道Id了。前面说的都是从父组件传递数据到子组件的方法，我们现在要从子组件传数据到父组件，有办法做到反向传回去吗？

有的，那就是`EventCallback`，但是要把`Delete`改成`<input>`而非`<MyButton>`，因为`EventCallback`是由子组件传向父组件，如果用`<MyButton>`，Id的流向就必须先这样`<Blog>` => `<Post>` => `<MyButton>`，接着再用`EventCallback`反向`<MyButton>` => `<Post>` => `<Blog>`，实在太麻烦了。

先把Delete按钮改成`<input>`，加入`@onclick="ReturnPostId"`。

![](https://img1.dotnet9.com/2021/12/2105.png)

接着在`PostBase.razor.cs`定义类型为`EventCallback<int>`的属性 `GetPostId`，记住一定要加上`[Parameter]`特性，因为这要被`<Blog>`调用。然后完整定义`ReturnPostId()`方法，里面做的就是`GetPostId.InvokeAsync(Post!.PostId);`，当外部传来的`GetPostId`被触发时，就将`Post.PostId`传给父组件也就是`<Blog>`。

![](https://img1.dotnet9.com/2021/12/2106.png)

再在`BlogBase.razor.cs`定义同名方法`GetPostId(int id)`，名字不需要一样，这边只是为了方便取同名，里面做的事情就是移除跟收到的Id有相同值的Post。

![](https://img1.dotnet9.com/2021/12/2107.png)

最后在`Blog.razor`的`<Post>`的`GetPostId`放入刚刚定义的方法就可以了。

![](https://img1.dotnet9.com/2021/12/2108.png)

我们来验证看看，先新增4条日志，再删除第2条，可以看到Id等于2的那条成功被删除了。

![](https://img1.dotnet9.com/2021/12/2109.gif)

除了EventCallback，还有Delegate可以使用，不过局限性较大，我们也来试试看。

先在`PostBase.razor.cs`定义类型为`Action<int>`的属性 `GetPostIdForDelegate`，`ReturnPostId()`改用`GetPostIdForDelegate`。

![](https://img1.dotnet9.com/2021/12/2110.png)

接着在`Blog.razor`的`<Post>`改用`GetPostIdForDelegate`。

![](https://img1.dotnet9.com/2021/12/2111.png)

但是实际点击后会发现不会删除日志，这是因为EventCallback会监控Component，一旦有变化就会重新渲染，委托则不会，委托必须在父组件也就是`BlogBase.razor.cs`调用`StateHasChanged();`方法，让父组件知道状态改变了。

![](https://img1.dotnet9.com/2021/12/2112.png)

另外委托一旦在子组件中定义了，父组件就必须要调用，否则会发生错误，EventCallback则没这问题。

![](https://img1.dotnet9.com/2021/12/2113.png)

**站长注：当然，善于使用nullable也可以避免这种异常：**

![](https://img1.dotnet9.com/2021/12/2114.png)

**引用：**

1. [Blazor EventCallback](https://www.pragimtech.com/blog/blazor/blazor-eventcallback/)
2. [EventCallback](https://docs.microsoft.com/en-us/aspnet/core/blazor/components/event-handling?view=aspnetcore-5.0#eventcallback-1)
3. [Blazor Tutorial - Ep11 - EventCallback and how it is different from delegate](https://www.youtube.com/watch?v=NQ7P0IPIqZk)

**注：本文代码通过 .NET 6 + Visual Studio 2022重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-16_03.md)