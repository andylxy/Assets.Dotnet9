---
title: (7/30)大家一起学Blazor：生命周期(Lifetime)
slug: 7-of-30-let-us-learn-blazor-together-lifetime
description: 自己建立的Service都必须在Startup.cs(Blazor Server)或Program.cs(Blazor WebAssemlby)注册，但有些基本Service就不用自己做了。
date: 2021-12-13 21:38:15
copyright: Reprint
author: StrayaWorker
originaltitle: (7/30)大家一起学Blazor：生命周期(Lifetime)
originallink: https://ithelp.ithome.com.tw/articles/10261219
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums: 学Blazor
categories: Blazor
tags: Blazor Server
---

前面说过自己建立的Service都必须在`Program.cs`注册，但有些基本Service就不用自己做了。

目前Blazor提供内建的Service有三个，分别为：

1. `HttpClient`：处理http请求，生命周期为Scoped(注意：只有Blazor WebAssembly有提供，Blazor Server必须自己注册)
2. `IJSRuntime`：提供Javascript runtime组件处理JS功能，Blazor WebAssembly生命周期为`Singleton`，Blazor Server生命周期为`Scoped`
3. `NavigationManager`：处理路由导向和状态，Blazor WebAssembly生命周期为`Singleton`，Blazor Server生命周期为`Scoped`

生命周期指的就是Component存活的时间，除了`Singleton`跟`Scoped`，还有一种`Transient`。

1. `Singleton`是指从程序启动到结束都只会有一个实例，所有Component共用一个实例。
2. `Transient`则是每次使用该Component时，都会产生一个新实例。
3. `Scoped`较为特别，Blazor Server跟Blazor WebAssembly模式不相同，Blazor Server的`Scoped`是指每次HTTP请求都会产生一个新的实例，但Component之间通过SingalR传递不会产生，微软文档说明「Blazor WebAssembly目前没有DI的概念，`Scoped`相当于`Singleton`」。

不过笔者当初看了上述说明也是很蒙，直到看了一个视频用GUID示范后才有所明白，我们就来试试看。

首先建立一个接口`IGuidService`，里面只有一个类型为string的属性`UId`，接着建立类`GuidService`并在构造函数中初始化属性UId为GUID字符串，再去`Program.cs`使用`AddTransient`注册。

![](https://img1.dotnet9.com/2021/12/1301.png)

然后建立一个`Guid.razor`Component，里面只有三行分别定义路由、注入服务以及显示GUID字符串，因为这案例很简单所以没用到ComponentBase，所以需要在`_Import.razor`加入`@using BlazorServer.Services`，最后为了切换方便，在`NavMenu.razor`定义一组NavLink指向刚才建立的`Guid.razor`。

![](https://img1.dotnet9.com/2021/12/1302.png)

启动后不论在Post及Guid页面切换，或是重新加载页面，都可以看到生成全新的一组GUID，这就是`Transient`的特性：每次切换都产生新的实例。

![](https://img1.dotnet9.com/2021/12/1303.gif)

接着将注册方式改为`Singleton`，可以看到就算重新加载网页，也都是同一组GUID，这就是Singleton的特性：程序启动到结束都只会有一个实例。

![](https://img1.dotnet9.com/2021/12/1304.gif)

最后将注册方式改为`Scoped`，切换到Post页面再切回来，还是同一组GUID，但重新加载页面时就会产生新的一组，这就是`Scoped`的特性：每次产生HTTP请求都会有新的实例，Component之间则不会产生新实例。

![](https://img1.dotnet9.com/2021/12/1305.gif)

上述的例子是以Blazor Server进行，若以Blazor WebAssembly进行，则`Singleton`会产生跟Blazor Server不同的情况，原因就是Blazor WebAssembly没有服务端，每次重新加载网页都会将程序下载到浏览器，这是一个全新的HTTP请求，所以`Singleton`跟`Scoped`都是只要一重新加载网页就会产生新的实例。

![](https://img1.dotnet9.com/2021/12/1306.png)

![](https://img1.dotnet9.com/2021/12/1307.png)

注：笔者为了方便省略视频中某些内容，有兴趣的人可以再研究

**引用：**

- [Blazor Course-Use ASP.NET Core to Build Full-Stack C# Web Apps](https://youtu.be/4G_BzLxa9Nw?t=15720)
- [ASP.NET Core Blazor dependency injection](https://docs.microsoft.com/en-us/aspnet/core/blazor/fundamentals/dependency-injection?view=aspnetcore-5.0&pivots=server)

**注：本文代码通过 .NET 6 + Visual Studio 2022重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-13_01.md)