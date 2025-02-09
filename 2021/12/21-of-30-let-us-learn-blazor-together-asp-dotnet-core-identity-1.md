---
title: (21/30)大家一起学Blazor：ASP.NET Core Identity(1)
slug: 21-of-30-let-us-learn-blazor-together-asp-dotnet-core-identity-1
description: 今天来实现身分验证的部分
date: 2021-12-22 23:01:31
copyright: Reprint
author: StrayaWorker
originaltitle: (21/30)大家一起学Blazor：ASP.NET Core Identity(1)
originallink: https://ithelp.ithome.com.tw/articles/10268982
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums: 学Blazor
categories: Blazor
tags: Blazor Server
---

今天来实现身分验证的部分，笔者此前是用`ASP.NET Core Web API` 搭配`Blazor`，用户第一次成功登录时，在后端将`Role`、`Claim` 等权限储存在`JWT`，将`JWT` 存在浏览器的`LocalStorage` 里面，前端再自己重写`AuthenticationStateProvider`，去检查`LocalStorage` 的`JWT`，接着将`AuthenticationState` 当作`CascadingParameter` 层层传递到各`Component`，这样就不需要不停跟后端交换数据，这是个很宝贵的经验，让笔者对身分验证有深入了解，这次笔者试试看`ASP.NET Core` 自己的`Identity`。

首先去NeGet 下载3 个组件，分别为`Microsoft.AspNetCore.Identity.EntityFrameworkCore`、`Microsoft.AspNetCore.Identity.UI`、`Microsoft.VisualStudio.Web.CodeGeneration.Design`，第1 个是`Identity` 必备组件，如果想自己实现`JWT` 的话，只需要下载第一个组件，再下载`JWT` 相关组件(`Microsoft.AspNetCore.Authentication.JwtBearer`)即可，后面2 个都是让`ASP.NET Core Identity`帮我们生成预置`Identity` 页面的组件。

接着去`BlazorServer.Models`的`AppDbContext`，将继承的`DbContext`改为`IdentityDbContext`，代表接下来用的`DB` 跟`Identity`有关系。

![](https://img1.dotnet9.com/2021/12/3201.png)

在`BlazorServer` 项目按右键，选择「`添加`」，选择「`新搭建基架的项目`」，切换到「`标识`」页签，选择`标识`。

![](https://img1.dotnet9.com/2021/12/3202.png)

![](https://img1.dotnet9.com/2021/12/3203.png)

勾选「`替代所有文件`」，`数据上下文类`选择`AppDbContext`，要注意的是，如果刚才没将继承的类别改成`IdentityDbContext`，就不会有`AppDbContext`可以选，必须点右边的「`+`」符号自己新增一个`IdentityDbContext`。
![](https://img1.dotnet9.com/2021/12/3204.png)

这时候有可能遇到这种「`FileUpload.OnInitializedAsync(): 没有找到适合的方法来重写`」的错误信息，这通常是`Visual Studio 的问题`，先将这里注释，重复一次上一段的做法就可以。

![](https://img1.dotnet9.com/2021/12/3205.png)

![](https://img1.dotnet9.com/2021/12/3206.png)

接着去`Program.cs`加上身分验证的服务，Identity 预置将验证信息存在Cookie。

![](https://img1.dotnet9.com/2021/12/3207.png)

接着在`程序包管理器控制台`执行两段命令，`Add-Migration IdentitySupport`新增`Migration`，`Update-Database`更新`DB`，去看数据库，可以看到`多了6 张表`，其中最常用到的就是`AspNetUsers`、`AspNetRoles` 及`AspNetUserRoles`，如果以`Claim` 处理权限的话，就会用到`AspNetUserClaims`。

![](https://img1.dotnet9.com/2021/12/3208.png)

项目则多了一个`Areas` 文件夹，里面就是`ASP.NET Core Identity` 的实现，包括了`登录系统`、`帐号系统`、`管理系统`。

![](https://img1.dotnet9.com/2021/12/3209.png)

我们去`NavMenu.razor`加上通往`Login` 的`NavLink`，在相对路径中`Areas` 跟`Pages` 可以省略。

```C#
<div class="nav-item px-3">
    <NavLink class="nav-link" href="Identity/Account/Login" Match="NavLinkMatch.All">
        <span class="bi bi-file-earmark-lock2 h4 p-2 mb-0" aria-hidden="true"></span> Login
    </NavLink>
</div>
```

启动网站后，从左边Nav 前往Login页面，可以看到已经有个完善的登录系统，包括注册、登录、忘记密码等等功能，就连注册密码的规则也有，我们照规则注册一个帐号，数据库也生成了刚刚注册的帐号。

![](https://img1.dotnet9.com/2021/12/3210.gif)

注册界面填写正确的邮箱和密码，`AspNetUsers`表即可查看注册的用户信息：

![](https://img1.dotnet9.com/2021/12/3211.png)

**引用**

1. [Claims-based authorization in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/claims?view=aspnetcore-5.0)
2. [Claim type and claim value in claims policy based authorization in asp net core](https://www.youtube.com/watch?v=I2wgxzLbESA)
3. [ASP NET Core Identity tutorial from scratch](https://www.youtube.com/watch?v=egITMrwMOPU&list=PL6n9fhu94yhVkdrusLaQsfERmL_Jh4XmU&index=65)
4. [Unable to resolve service for type IEmailSender while attempting to activate RegisterModel](https://stackoverflow.com/questions/52089864/unable-to-resolve-service-for-type-iemailsender-while-attempting-to-activate-reg)

**注：本文代码通过 .NET 6 + Visual Studio 2022重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-22_02.md)