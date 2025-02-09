---
title: 如何处理EF Core的多对多关系？
slug: how-to-handle-many-to-many-relationships-with-ef-Core
description: 多对多关系不像其他关系那么简单，在这篇文章中，我将向您展示如何创建多对多关系以及如何在 EF Core 中使用它们。
date: 2021-11-02 20:47:54
copyright: Reprint
author: Zbigniew
originaltitle: 如何处理EF Core的多对多关系？
originallink: https://softdevpractice.com/blog/many-to-many-ef-core/
draft: False
cover: https://img1.dotnet9.com/2021/11/cover_05.jpeg
categories: EF Core
tags: ORM,EF Core
---

![](https://img1.dotnet9.com/2021/11/cover_05.jpeg)

多对多关系不像其他关系那么简单，在这篇文章中，我将向您展示如何创建多对多关系以及如何在 EF Core 中使用它们。

## 模型

多对多的简单而实用的例子可能是某种数字电子商务商店。用户可以将商品放入购物车（一个购物车可以有多个商品），而商品属于多个购物车。让我们从创建`Cart`和`Item`类开始。

```C#
public class Cart
{
    public int Id { get; set; }

    public ICollection<Item> Items { get; set; }
}
```

```C#
public class Item
{
    public int Id { get; set; }

    public string Name { get; set; }

    public int Quantity { get; set; }

    public ICollection<Cart> Carts { get; set; }
}
```

这看起来不错，但它不起作用。在本文发表时，EF Core 无法处理这种情况。看起来EF Core不知道如何处理这种关系，当您尝试添加迁移时，您会得到以下结果：

>>Unable to determine the relationship represented by navigation property ‘Cart.Items’ of type ‘ICollection<Item>’. Either manually configure the relationship, or ignore this property using the ‘[NotMapped]’ attribute or by using ‘EntityTypeBuilder.Ignore’ in ‘OnModelCreating’.
>>
>>【无法确定类型为“ICollection&lt;Item&gt;”的导航属性“Cart.Items”表示的关系。手动配置关系，或使用“[NotMapped]”属性或使用“OnModelCreating”中的“EntityTypeBuilder.Ignore”忽略此属性。】

我们需要做的第一件事是手动创建另一个“中间”类（表），它将建立`Cart`和`Item`的多对多关系,让我们创建这个类：

```C#
public class CartItem
{
    public int CartId { get; set; }
    public Cart Cart { get; set; }

    public int ItemId { get; set; }
    public Item Item { get; set; }
}
```

我们创建了关联`Cart`和`Item`的新类`CartItem`，我们还需要更改它们各自的导航属性：

```C#
public class Cart
{
    public int Id { get; set; }

    public ICollection<CartItem> Items { get; set; }
}
```

```C#
public class Item
{
    public int Id { get; set; }

    public string Name { get; set; }

    public int Quantity { get; set; }

    public ICollection<CartItem> Carts { get; set; }
}
```

如果您现在尝试添加迁移，则会出现另一个错误：

>>The entity type ‘CartItem’ requires a primary key to be defined.
>>
>>【实体类型“CartItem”需要定义一个主键。】

对，`CartItem`没有主键, 由于它是多对多关系，因此它应该具有复合主键。复合主键类似于常规主键，但它由两个属性（列）而不是一个属性组成。目前，创建复合键的唯一方法是在`OnModelCreating`.

```C#
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);

    builder.Entity<CartItem>().HasKey(i => new { i.CartId, i.ItemId });
}
```

最后，我们的数据库结构可以由 EntityFramework 处理,我们可以继续迁移了。

## 插入多对多

假设我们已经有`Cart`和`Item`在我们的数据库中,现在我们想将特定商品(`Item`)添加到特定购物车(`Cart`)，为了做到这一点，我们需要创建新的CartItem并保存它。

```C#
var cart = db.Carts.First(i => i.Id == 256);
var item = db.Items.First(i => i.Id == 1024);

// 可以使用两个类的主键ID进行关联
var cartItem = new CartItem
{
    CartId = cart.Id,
    ItemId = item.Id
};

// 也可以使用两个类实体进行关联
var cartItem = new CartItem
{
    Cart = cart,
    Item = item
};

db.Add(cartItem);
db.SaveChanges();
```

## 在多对多中检索相关数据

从数据库中获取数据相当简单，注意使用`Include`关联检索相关数据。这里总共涉及3个表：`Cart`, `Item`, `CartItem`(将商品`Item`与购物车`Cart`关联起来)。

```C#
// 获取关联所有商品的指定购物车
var cartIncludingItems = db.Carts.Include(cart => cart.Items).ThenInclude(row => row.Item).First(cart => cart.Id == 1);
// 获取指定购物车的所有商品
var cartItems = cartIncludingItems.Items.Select(row => row.Item);
```

另外，有些操作可以不使用关系来执行。例如，如果您有购物车ID，则可以使用以下 Linq 一次获取所有商品：

```C#
var cartId = 1;
var cartItems = db.Items.Where(item => item.Carts.Any(j => j.CartId == cartId));
```

相同的原则适用于相反的用例，这意味着您可以应用上述模式来获取具有特定项目的所有购物车。

## 从多对多中删除

删除是指删除购物车`Cart`和商品`Item`之间的关系`CartItem`。在以下示例中，我们不会删除购物车`Cart`或商品`Item`，只会删除购物车`Cart`和商品`Item`之间的关系`CartItem`。

让我们从购物车`Cart`中删除单个产品`Item`开始。

```C#
var cartId = 1;
var itemId = 1;
var cartItem = db.CartsItems.First(row => row.CartId == cartId && row.ItemId == itemId);

db.Remove(cartItem);
db.SaveChanges();
```

然后，让我向您展示如何从购物车中删除所有项目。

```C#
var cart = db.Carts.Include(c=> c.Items).First(i => i.Id == 2);

db.RemoveRange(cart.Items);
db.SaveChanges();
```

---

>
>原文作者：Zbigniew
>
>原文标题：How to handle Many-To-Many in Entity Framework Core
>
>原文链接：https://softdevpractice.com/blog/many-to-many-ef-core/
>
>翻译：沙漠尽头的狼

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/11/2021-11-02_02.md)