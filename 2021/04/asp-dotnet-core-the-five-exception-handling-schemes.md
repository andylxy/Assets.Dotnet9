---
title: ASP.NET Core 5种异常处理方案
slug: asp-dotnet-core-the-five-exception-handling-schemes
description: 异常处理在编程中非常重要，一来可以给用户友好提示，二来也是为了程序安全。
date: 2021-04-29 09:27:26
copyright: Reprint
author: .NET技术栈
originaltitle: ASP.NET Core 5种异常处理方案
originallink: https://ut32.com/post/handle-exception
draft: False
cover: https://img1.dotnet9.com/2021/04/cover_01.jpg
categories: .NET相关
tags: ASP.NET Core,异常
---

异常处理在编程中非常重要，一来可以给用户友好提示，二来也是为了程序安全。在Asp.Net Core中，默认已经为我们了提供很多解决方案，下面就来总结一下。

## 1. 继承Controller，重写OnActionExecuted

使用vs新建controller时，默认都会继承一个Controller类，重写OnActionExecuted，添加上异常处理即可。一般情况下我们会新建一个BaseController, 让所有Controller继承BaseController。代码如下

```C#
public class BaseController : Controller
{
    public override void OnActionExecuted(ActionExecutedContext context)
    {
        var exception = context.Exception;
        if (exception != null)
        {
            context.ExceptionHandled = true;
            context.Result = new ContentResult
            {
                Content = $"BaseController错误 : { exception.Message }"
            };
        }
        base.OnActionExecuted(context);
    }
}
```

这种处理方式优点当然是简单，缺点也很明显，如果cshtml页面抛错，就完全捕获不了。当然如果项目本身就是一个web api 项目，没有view，这还是可以的。

## 2. 使用 ActionFilterAttribute

ActionFilterAttribute是一个特性，本身实现了 IActionFilter 及 IResultFilter , 所以不管是action里抛错，还是view里抛错，理论上都可以捕获。我们新建一个 ExceptionActionFilterAttribute, 重写 OnActionExecuted及OnResultExecuted，添加上异常处理，完整代码如下。

```C#
public class ExceptionActionFilterAttribute:ActionFilterAttribute
{
    public override void OnActionExecuted(ActionExecutedContext context)
    {
        var exception = context.Exception;
        if (exception != null)
        {
            context.ExceptionHandled = true;
            context.Result = new ContentResult
            {
                Content = $"错误 : { exception.Message }"
            };
        }
        base.OnActionExecuted(context);
    }

    public override void OnResultExecuted(ResultExecutedContext context)
    {
        var exception = context.Exception;
        if (exception != null)
        {
            context.ExceptionHandled = true;
            context.HttpContext.Response.WriteAsync($"错误 : {exception.Message}");
        }
        base.OnResultExecuted(context);
    }
}
```

使用方式有两种，

1. 在controller里打上 [TypeFilter(typeof(ExceptionActionFilter)] 标签。
2. 在Startup里以filter方式全局注入。

```C#
services.AddControllersWithViews(options =>
{
    options.Filters.Add<ExceptionActionFilterAttribute>();
})
```

## 3. 使用 IExceptionFilter

我们知道, Asp.Net Core提供了5类filter, IExceptionFilter是其中之一，顾名思义，这就是用来处理异常的。Asp.net Core中ExceptionFilterAttribute已经实现了IExceptionFilter，所以我们只需继承ExceptionFilterAttribute，重写其中方法即可。 同样新建CustomExceptionFilterAttribute继承 ExceptionFilterAttribute，重写 OnException ，添加异常处理，完整代码如下

```C#
public class CustomExceptionFilterAttribute : ExceptionFilterAttribute
{
    public override void OnException(ExceptionContext context)
    {
        context.ExceptionHandled = true;
        context.HttpContext.Response.WriteAsync($"CustomExceptionFilterAttribute错误:{context.Exception.Message}");
        base.OnException(context);
    }
}
```

注意，ExceptionFilterAttribute还提供了异步方法OnExceptionAsync，这两个处理方法，只需重写一个即可，如果两个都重写，两个方法都会执行一次。

使用方式有两种，

1. 在controller里打上 [CustomExceptionFilter] 标签。
2. 在Startup里以filter方式全局注入。

```C#
services.AddControllersWithViews(options =>
{
    options.Filters.Add<CustomExceptionFilterAttribute>();
})
```

## 4. 使用ExceptionHandler

在 startup 里，vs新建的项目会默认加上

```C#
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
else
{
    app.UseExceptionHandler("/Home/Error");
}
```

上边这段代码，大意为：开发环境使用app.UseDeveloperExceptionPage(); 。生产环境会跳转至 home/error 页面。

UseDeveloperExceptionPage 会给出具体错误信息，具体包括 Stack trace、Query、Cookies、Headers 四部分。.net core 3.1后有些改动，如果请求头加上 accept:text/html, 就返回html页面，不加的话，只会返回错误。

注意：app.UseDeveloperExceptionPage(); 应该置于最前边。

```C#
if (!env.IsDevelopment())
 {
     app.UseDeveloperExceptionPage();
 }
 else
 {
     app.UseExceptionHandler(builder =>
     {
         builder.Run(async context =>
         {
             var exceptionHandlerPathFeature = context.Features.Get<IExceptionHandlerPathFeature>();
             await context.Response.WriteAsync($"error:{exceptionHandlerPathFeature.Error.Message}");
         });
     });
 }
 ```
 
## 5. 自定义Middleare处理

通过middleware全局处理。

```C#
public class ErrorHandlingMiddleware
{
   private readonly RequestDelegate next;

   public ErrorHandlingMiddleware(RequestDelegate next)
   {
        this.next = next;
   }

   public async Task Invoke(HttpContext context)
   {
        try
        {
           await next(context);
        }
        catch (System.Exception ex)
        {
           //处理异常
        }
   }
}
```

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/04/2021-04-29_01.md)