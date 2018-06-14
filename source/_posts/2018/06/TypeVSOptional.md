---
title: Type? VS Optional
date: 2018/06/13
categories: 编程语言
tags:
- Scala
---
最近阅读一些关于 Kotlin 类型系统方面的书，发现 Kotlin 的类型系统针对 `null` 有着独特的设计哲学。在 Java 或者其它编程语言中，经常会出现 `NullPointerException`，而导致此异常的重要原因是因为你可以写 `String s = null` 这样的代码。其实可以认为这是 Java 等语言类型系统设计的一个缺陷，它们允许 `null` 可以作为任何类型的值！

但是在 Kotlin 中，如果你声明 `val s: String = null`，那么编译器会给你一个 error，因为在 Kotlin 中，你不允许把一个 `null` 值赋给一个普通的类型。如果你声明一个这样的函数 `fun strLen(s: String) = {...}`，那么这个函数将不接受值为 `null` 的参数。

这个设计看起来如此的美好，他可以极大程度的减少 Kotlin 产生 `NullPointerException`，可是如果有一天，你需要写一个函数，接收一个值可能为 `null` 的 `String` 类型，那么在 Kotlin 中你可以声明一个可空的字符串类型：`String?`。`fun strLen(s: String?) = {...}` 此时 Kotlin 的编译器会让这行代码通过。

可空类型(`Type?`)的设计，是 Kotlin 另一个设计哲学，它要求工程师在设计的时候就需要确定该变量是否可为空。如果不为空就使用`Type` 类型声明，否则就使用 `Type?` 类型声明。这让我想起在 Scala 中存在一个和 `Type?` 有着异曲同工之妙的一个类型—— `Option[T]`。

`Option[T]` 有两个子类型：`Some[T]` 和 `None`，你可以使用 `val s: Option[String] = Some("123")` 来表示一个字符串存在，当然你可以使用`val s: Option[String] = None` 来表示这个字符串不存在。

Scala 和 Kotlin 都是基于 JVM 的编程语言，而 `Option[T]` 和 `Type?` 的设计就是用来解决 JVM 平台出现的 `NullPointerException`。但二者的设计理念却截然不同，Scala 的 `Option[T]` 是在原有类型基础上使用 `Option` 做一层封装，而 Koltin 的 `Type?` 是使用语法糖完成的。
