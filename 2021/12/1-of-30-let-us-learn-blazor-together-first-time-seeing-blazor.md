---
title: (1/30)大家一起学Blazor：初见Blazor
slug: 1-of-30-let-us-learn-blazor-together-first-time-seeing-blazor
description: 笔者接触软件行业的时间不长，先后接触三种架构
date: 2021-12-09 22:35:19
copyright: Reprint
author: StrayaWorker
originaltitle: (1/30)大家一起学Blazor：初见Blazor
originallink: https://ithelp.ithome.com.tw/articles/10258944
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums: 学Blazor
categories: Blazor
tags: Blazor,ASP.NET Core
---

笔者接触软件行业的时间不长，先后接触三种架构，分别为ASP.NET MVC、ASP.NET Core & Blazor、ASP.NET Core & Angular，由于ASP.NET MVC 是笔者初入软件行业的新人时期，迷迷糊糊地就在前辈的带领下完成了项目，所以没什么感悟，只是大概了解前后端的差别，前端以HTTP Request 发送向后端取数据，后端回传数据，前端再将结果呈现在页面上。

待到项目收尾，在主管力推转型之下改用ASP.NET Core & Blazor 并指派笔者做出模板，笔者搜寻网路资源东拼西凑摸索出了一套堪用的架构，当时只觉得Blazor 跟ASP.NET MVC 差距甚大，前端不需要弱类型的Javascript，前后端都是强类型的世界，对笔者的懒人个性起到莫大帮助，可惜接下来由于客户需求，只能改用ASP.NET Core & Angular，没办法深入研究Blazor。

不过有失必有得，在新项目的高压强度及同事的指导下，笔者大致理解了Angular 的Module, Component 分层架构、Observable 类似Ajax 的概念，虽然仍是一知半解，但笔者也对Angular 大致熟悉了，发现Component 的概念跟Blazor 的相似，可说是殊途同归。

由于笔者参加铁人赛的目的在于纪录心得，因此有些地方的描述会较为繁琐，另外笔者从事软件行业经验并不丰富，若笔者文章内容有误，也请各位不吝赐教。

笔者这次铁人赛想要用Blazor完成一个可以供使用者输入日志的网站，预计涵盖的项目大概会有：

1. Blazor 简介
2. Blazor Server, Blazor WebAssembly 2 种Hosting 模式及项目结构
3. Component 组件介绍、事件处理
4. ASP.NET Core
5. EF Core
6. 登录、授权

Blazor 使用C# 编写，虽然也可以用VB、F# 编写，但笔者只熟习C#，C# 属于.NET 框架，.NET 框架在Visual Studio 开发较为方便，因此笔者会使用Visual Studio 开发，版本为.NET 5。关于ASP.NET, .NET Framework, NET Core 的差别，笔者此前看到有人详述，觉得非常有帮助，若时间允许，笔者会再分享出来。另外笔者过程会使用git 做为版本控制工具，最终程序代码会放在github 上。

[Github link](https://github.com/Tommy95271/BlazorPractice)

[Visual Studio download Link](https://visualstudio.microsoft.com/zh-hant/vs/whatsnew/)

**注：本文代码通过 .NET 6 + Visual Studio 2022重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-09_02.md)