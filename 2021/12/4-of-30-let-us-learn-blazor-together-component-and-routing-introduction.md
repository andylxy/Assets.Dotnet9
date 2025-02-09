---
title: (4/30)大家一起学Blazor：Component及路由介绍
slug: 4-of-30-let-us-learn-blazor-together-component-and-routing-introduction
description: 由于笔者当初是用ASP.NET Core API + Blazor Server，所以会以Blazor Server示范，日后研究完Blazor WebAssembly会再将心得补上。
date: 2021-12-10 23:01:39
copyright: Reprint
author: StrayaWorker
originaltitle: (4/30)大家一起学Blazor：Component及路由介绍
originallink: https://ithelp.ithome.com.tw/articles/10260047
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums: 学Blazor
categories: Blazor
tags: Blazor Server
---

由于笔者当初是用ASP.NET Core API + Blazor Server，所以会以Blazor Server示范，日后研究完Blazor WebAssembly会再将心得补上。

首先既然Component是可以重复利用的，我们在Index.razor放上两个Counter，启动项目(如果不想完整调试，可以按ctrl+F5，就会启动不调试模式，启动速度比较快，而且每次储存文件，Blazor都会监测到，网页重新加载就可以载入新程序了)，浏览器上两个Counter有各自的Click me按钮，分别点击后可以看到数字分别增加，代表是不同的Component，那这些数字又定义在哪里呢？

打开Counter.razor，最上面是@page指示词，这个稍后再说。再来是html跟一些C#程序，最后是@code区块，这就是Blazor的奇妙之处了，@code相当于一般网页JS做的事情诸如定义变量、实现方法、发送request到后端或是API，不过Blazor用C#编写，这里定义了一个私有变量currentCount，还有一个方法IncrementCount()，调用这方法的是Click me按钮，每一次点击按钮都会使currentCount+1，而呈现结果就在p元素内。

![Index.razor和Counter.razor](https://img1.dotnet9.com/2021/12/0801.png)

![两个Counter独立](https://img1.dotnet9.com/2021/12/0802.gif)

currentCount定义的方式跟页面呈现就是一种模型绑定(model binding)，意思是数据跟页面有绑定关系，.NET Framework的View的@model或是@Viewbag，Angular的[(ngModel)]也是同理，都是要做到数据流到页面后，对页面操作可以影响数据的行为。

我们来定义另一个变量myClass，给这变量一些bootstrap的class，再把变量放在button的class里面，记住在html里面用到C#的程序必须以`@`开头，不然Blazor不知道要编译。重新加载页面可以看到按钮的样式变了，Blazor帮我们把myClass的值text-primary bg-warning放进button的class。

![添加myClass到Counter按钮](https://img1.dotnet9.com/2021/12/0803.png)

接着我们看FetchData.razor，这里看到了@using BlazorServer.Data，我们待会可以把这个using放进_import.razor，那么@inject WeatherForecastService ForecastService又是什么呢？我们先看@code区块，看到这里定义了WeatherForecast数组类型的变量forecasts，且用异步方法OnInitializedAsync调用了ForecastService.GetForecastAsync(DateTime.Now)，将结果回传forecasts，眼尖的人应该发现了最上面的ForecastService跟@code区块的ForecastService一模一样。

![FetchData.razor](https://img1.dotnet9.com/2021/12/0804.png)

我们点一下GetForecastAsync()方法并按下F12，可以看到这个方法回传的就是5个随机产生的天气数据阵列，html里面有判断forecasts是否为null，不是的话就产生一个table，里面用foreach将forecasts的日期、摄氏、华氏及天气状态一一呈现出来。

![Service生成数据及渲染](https://img1.dotnet9.com/2021/12/0805.png)

前面说过Blazor只有一个网页，其他内容都是一个个Component组成的，每次触发事件，Server或是WebAssemlby都会将相应Component呈现在浏览器上，但Blazor怎么知道现在要呈现哪个Component呢？

原因就是@page指示词，这个指示词相当于传统的路由，可以看到Index.razor的@page 为"/"，表示这是首页，Counter.razor及FetchData.razor也有相应的@page指示词。一个页面可以有多个@page指示词，不过开头`一定要有斜线且用双引号`包起来，笔者曾想过建立enum集中管理不同Component的@page，可惜目前Blazor不支持这种做法。另外若两个Component用了相同的@page，编译阶段就会出现错误提示，所以也不用担心若有重复路由Blazor会怎么处理。

![@page指示词](https://img1.dotnet9.com/2021/12/0806.png)

那么左边菜单的Home, Counter, Fetch data页面又是在哪里定义的呢？打开MainLayout.razor，可以看到NavMenu元素，再打开NavMenu.razor，可以看到三个NavLink Component，这些Component会被Server翻译为浏览器认识的a元素，因此就算我们打开Dev tool，也只会看到a元素。

![左侧菜单](https://img1.dotnet9.com/2021/12/0807.png)

![左侧菜单在html呈现为a标签1](https://img1.dotnet9.com/2021/12/0808.gif)

![左侧菜单在html呈现为a标签2](https://img1.dotnet9.com/2021/12/0809.png)

回到MainLayout.razor，可以看到@Body指示词，这就是其他Component会放置的地方，可以说是种placeholder，再看App.razor里面有Found及NotFound两个Component，从字面看就知道，前者是当输入的网址找到匹配的Component则会进入这里，后者则是找不到匹配的Component，可以看到两者都用了MainLayout。另外若有不同页面要套用不同Layout，也可以自己定义。

![@body](https://img1.dotnet9.com/2021/12/0810.png)

![](https://img1.dotnet9.com/2021/12/0811.png)

说到这里，我们复习一下Blazor Server是怎么走的，可以看到跟Angular类似都是一层一层往下，管理较为方便。Blazor WebAssemlby跟Blazor Server的index.html跟_Layout.cshtml大致相等，以及缺少了appsettings.json文件，通常会将程序跟数据库连接需要的连线字串放在这个文件，可证Blazor WebAssemlby确实只是被动接收数据，而无法主动跟数据库连接，笔者曾试过在这里引用EF Core，也是无法让Blazor WebAssemlby接触数据库，在.NET Framework的世界是用XML格式的web.config，在.NET Core则改用JSON格式的appsettings.json。

引用: [ASP NET Core blazor project structure](https://www.youtube.com/watch?v=1MkPWOiwLIM)

**注：本文代码通过 .NET 6 + Visual Studio 2022重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-10_02.md)