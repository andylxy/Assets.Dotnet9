---
title: .NET Core 简单且高级的库 csredis
slug: dotnet-simple-and-advanced-library-csredis
description: 开源免费的redis包
date: 2021-04-11 09:50:47
copyright: Reprint
author: FreeSql & CSRedis
originaltitle: .NET Core 简单且高级的库 csredis
originallink: https://www.cnblogs.com/kellynic/p/9803314.html
draft: False
cover: https://img1.dotnet9.com/2021/04/cover_05.png
categories: .NET相关
tags: .NET,Redis,csredis
---

## 1 前言

.NET Core 从1.0发布历经坎坷，一开始各种库缺失到现在的部分完善，走到今天实属不易。

比如 redis-cli SDK 简直是坑出不穷。

过去 .net 最有名望的 `ServiceStack.Redis` 早已沦为商业用途，在 .NETCore 中使用只能充值；后来居上的 `StackExchange.Redis` 虽然能用，但线上各种 Timeout 错误把人坑到没脾气，两年多都不解决，最近发布的 2.0 版本不知道是否彻底解决了底层。

## 2 csredis v3.0.0更新

1. 所有方法名与redis-cli保持一持；

据了解，java/python/go/nodejs/php SDK 方法名基本都与 redis-cli 一致，反对二次命名的库

2. 增加反序列对象获取，如：Get<byte[]>、HGet<byte[]>，所有获取方法都重载了，默认获取仍然是string；

3. SafeObjectPool 的引入使用；

## 3 使用

```shell
nuget Install-Package CSRedisCore
```

```C#
var rds = new CSRedis.CSRedisClient("127.0.0.1:6379,password=123,defaultDatabase=13,poolsize=50,ssl=false,writeBuffer=10240,prefix=key前辍");
rds.Set("test1", "123123", 60);
rds.Get("test1");
//函数名与 redis-cli 的命令相同，rds 一定是单例单例单例
```

## 4 高级玩法：分区

现实多个服务节点共同分担存储，与官方的分区、集群、高可用方案不同。

>例如：缓存数据达到500G，如果使用一台redis-server服务器光靠内存存储将非常吃力，使用硬盘又影响性能。
>
>可以使用此功能自动管理N台redis-server服务器分担存储，每台服务器只需约 (500/N)G 内存，且每台服务器匀可以配置官方高可用架构。

```C#
var rds = new CSRedis.CSRedisClient(null,
  "127.0.0.1:6371,password=123,defaultDatabase=11,poolsize=10,ssl=false,writeBuffer=10240,prefix=key前辍", 
  "127.0.0.1:6372,password=123,defaultDatabase=12,poolsize=11,ssl=false,writeBuffer=10240,prefix=key前辍",
  "127.0.0.1:6373,password=123,defaultDatabase=13,poolsize=12,ssl=false,writeBuffer=10240,prefix=key前辍",
  "127.0.0.1:6374,password=123,defaultDatabase=14,poolsize=13,ssl=false,writeBuffer=10240,prefix=key前辍");
//实现思路：根据key.GetHashCode() % 节点总数量，确定连向的节点
//也可以自定义规则(第一个参数设置)

rds.MSet("key1", 1, "key2", 2, "key3", 3, "key4", 4);
rds.MGet("key1", "key2", "key3", "key4");
```

## 5 高级玩法：发布订阅

```C#
//普通订阅
rds.Subscribe(
  ("chan1", msg => Console.WriteLine(msg.Body)),
  ("chan2", msg => Console.WriteLine(msg.Body)));

//模式订阅（通配符）
rds.PSubscribe(new[] { "test*", "*test001", "test*002" }, msg => {
  Console.WriteLine($"PSUB   {msg.MessageId}:{msg.Body}    {msg.Pattern}: chan:{msg.Channel}");
});
//模式订阅已经解决的难题：
//1、分区的节点匹配规则，导致通配符最大可能匹配全部节点，所以全部节点都要订阅
//2、本组 "test*", "*test001", "test*002" 订阅全部节点时，需要解决同一条消息不可执行多次

//发布
rds.Publish("chan1", "123123123");
//无论是分区或普通模式，rds.Publish 都可以正常通信
```

## 6 高级玩法：缓存壳

```C#
//不加缓存的时候，要从数据库查询
var t1 = Test.Select.WhereId(1).ToOne();

//一般的缓存代码，如不封装还挺繁琐的
var cacheValue = rds.Get("test1");
if (!string.IsNullOrEmpty(cacheValue)) {
	try {
		return JsonConvert.DeserializeObject(cacheValue);
	} catch {
		//出错时删除key
		rds.Remove("test1");
		throw;
	}
}
var t1 = Test.Select.WhereId(1).ToOne();
rds.Set("test1", JsonConvert.SerializeObject(t1), 10); //缓存10秒

//使用缓存壳效果同上，以下示例使用 string 和 hash 缓存数据
var t1 = rds.CacheShell("test1", 10, () => Test.Select.WhereId(1).ToOne());
var t2 = rds.CacheShell("test", "1", 10, () => Test.Select.WhereId(1).ToOne());
var t3 = rds.CacheShell("test", new [] { "1", "2" }, 10, notCacheFields => new [] {
  ("1", Test.Select.WhereId(1).ToOne()),
  ("2", Test.Select.WhereId(2).ToOne())
});
```

## 7 高级玩法：管道

使用管道模式，打包多条命令一起执行，从而提高性能。

```C#
var ret1 = rds.StartPipe().Set("a", "1").Get("a").EndPipe();
var ret2 = rds.StartPipe(p => p.Set("a", "1").Get("a"));

var ret3 = rds.StartPipe().Get("b").Get("a").Get("a").EndPipe();
//与 rds.MGet("b", "a", "a") 性能相比，经测试差之毫厘
```

## 8 高级玩法：多数据库

如果确定一定以及肯定非要有切换数据库的需求，请看以下代码：

```C#
var connectionString = "127.0.0.1:6379,password=123,poolsize=10,ssl=false,writeBuffer=10240,prefix=key前辍";
var redis = new CSRedisClient[14]; //定义成单例
for (var a = 0; a< redis.Length; a++) redis[a] = new CSRedisClient(connectionString + "; defualtDatabase=" + a);

//访问数据库1的数据
redis[1].Get("test1");
```

## 9 性能比拼

![](https://img1.dotnet9.com/2021/04/0501.png)

## 10 结尾

依然是支持开源，感谢观看！

csredis 源码地址： https://github.com/2881099/csredis

>***本文作者***：FreeSql & CSRedis
>
>***本文链接***：https://www.cnblogs.com/kellynic/p/9803314.html
>
>***关于博主***：评论和私信会在第一时间回复。或者直接私信我。
>
>***版权声明***：本博客所有文章除特别声明外，均采用 BY-NC-SA 许可协议。转载请注明出处！
>
>***声援博主***：如果您觉得文章对您有帮助，可以点击文章右下角【推荐】一下。您的鼓励是博主的最大动力！

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/04/2021-04-11_01.md)