---
title: 从 Java 到 Scala（一）：面向对象谈起
date: 2018/02/10
categories: 编程语言
tags:
- Java2Scala
---
## 背景
三个月前，我加入水滴团队，面试中，面试官问：“你了解 Scala 吗？”

“不了解(尴尬)。”

“你知道 Spark 吗？它就是使用 Scala 编写的，不过在我们团队中，Scala 主要作为后端语言，我们 90% 以上的业务代码都是使用 Scala 编写。Scala 在国内使用的比较少，但是在国外用的还是蛮多的，如 Twitter 就是使用 Scala 写的后端。”

“你知道 Scala 这门编程语言产生的背景吗？它是由德国的计算机科学家和编程方法教授 [Martin Odersky](https://en.wikipedia.org/wiki/Martin_Odersky) 设计出来的，它的设计原理严格遵循数学的逻辑推理。因此它是一门优秀的编程语言，它不仅仅在工业界被广泛使用，在学术界也占用很高的研究地位。”

“你知道 Scala 与 Java 最主要的区别是什么吗？ Scala 和 Java 都基于 JVM，因此 Java 优秀的类库，Scala　都可以直接使用。但是 Scala 不仅仅是「面向对象」，它还拥抱「函数式」，尤其是它的「高阶」。”

“「高阶」现在你可能还理解不了，打个比方吧，如果我们把「面向对象」比作站在地面上观察事物的原理，并且使用这些原理解决问题，那么「高阶」就是让你站在山上去看待事物，对问题进行更高层次的**抽象**。”

“因此不管是解决实际问题，还是提高对编程语言的认知，Scala 都是一们值得学习的语言。”

于是从加入水滴的第一天，我就开始学习这门传说中很厉害的 Scala。我的参数书就是[《快学 Scala》。](https://book.douban.com/subject/19971952/)

当我看完这本书的时候，我发现我对 Scala 的基本用法有了大致的了解，但是对传说中的「高阶」还是不懂。 

这时我的实习导师 [Yison](https://github.com/yison-dripower) 说：“现在市场上的 Scala 教程，大多数都是按照模块来介绍 Scala，我们能不能出一个「从 Java 到 Scala 系列」，我们寻找一棵从 Java 通往 Scala 的知识树，通过对知识树的讲解，我们来学习 Scala。”

既然 Java 和 Scala 都是「面向对象」的，那我们这个系列的第一篇就来探索一下什么是「面向对象」？
<!-- more -->
## 模板和对象
「模板」是在代码层面描述一类对象的「行为」或者「状态」的**代码**，它是抽象的。如 Java 中的类，C 语言中的结构体，它们都是「模板」。

「对象」是在运行期间通过模板在内存中生成的一个个**实体**，它是具体的。如 Java 在运行期间通过 new 在内存中产生的实体就叫做「对象」。

如果你说共享单车，那么它就是一个「模板」；如果你说这辆共享单车和那辆共享单车，那它们就是「对象」。

在代码层面，「对象」的行为可以定义为「方法」，「对象」的状态可以定义为「属性」，那我们如何去描述一类「对象」的方法或者属性呢？－**封装**。

例如共享单车，它有车轮，二维码等属性，有开锁和关锁等行为。那么我们可以有三种方式来封装共享单车。
### 基于对象的封装
这种方式就是直接封装，最典型的例子就是 C 语言中的结构体。
封装共享单车的「模板」如下：

```C
struct SharedBicycle{
  车轮;
  二维码;
  开锁;
  关锁;
}; 
```
### 基于类的封装
大多数「面向对象」的语言，如 Java，Scala，C++等，都使用这种方式封装，「模板」如下：
```Scala
class SharedBicycle{
  属性：车轮;
  属性：二维码;
  方法：开锁;
  方法：关锁;
}
```
### 基于原型的封装
JavaScript 就是使用这种封装方式，「模板」如下：
```javascript
function SharedBicycle(){
  this.车轮 = xxx;
  this.二维码 = xxx;
}
//添加原型方法
SharedBicycle.prototype.开锁 = function(){...};
SharedBicycle.prototype.关锁 = function(){...};
```
### 编程语言分类
如果我们把上面的三种封装方式和「动态类型」、「静态类型」相结合，就可以把我们熟知的编程语言归类。

|      | 动态类型     | 静态类型   |
| :-------: | :--------: | :---: |
| 基于对象的封装 |   | C |
| 基于类的封装    | PHP    | Scala，Java，C++   |
| 基于原型的封装     |  JavaScript    |   　|

## 面向对象
「面向对象」是一种**程序设计的思路**，它要求工程师以**对象(模板)为最小单位**进行程序设计。当对象设计完成，程序设计也就完成了。

如果我们现在有一个「人骑共享单车」的业务，我们分别使用「命令式程序设计」和「面向对象的程序设计」来对比实现。
### 命令式的程序设计
命令式的程序设计要求我们一步一步的设计程序，当前程序设计完成以后，才能完成下一步的程序设计，因此伪代码如下：
1. 双手扶好车把手。
2. 检查刹车。
3. 把脚放在踏板上。
4. 进行起步。
5. 成功骑上共享单车。

### 面向对象的程序设计
「面向对象」的程序设计要求我们以「对象」为最小的设计单位，因此我们需要设计两个「对象」：「人」和「共享单车」。
人有骑共享单车的行为(前提是他会骑车)，共享单车有自己的属性和行为，因此，我们的「模板」如下(这里使用class进行封装，其它封装方法也可以)：

```Scala
class 共享单车{
  xxx 属性
  zzz 属性
  ...
  yyy 方法
  ...
}
```

```Scala
class 人{
  aaa 属性
  ...
  runBicycle(共享单车 b){
    ...
  }
}
```
当我们设计完对成，在运行期间，只需要调用 `人.runBicycle(共享单车)` 即可实现人骑共享单车的业务(这里的「人」和「共享单车」指的是运行期间具体的对象)。

「面向对象」的设计思路要求工程师把精力集中在**对象的设计**上，尤其是在团队合作的项目中，如果有人把我需要的对象设计好，我可以直接拿过来使用！
### 纯粹的面向对象
我们知道 Java 是一门「面向对象」的语言，那么在 Java 中是否真的「万物皆对象」？	

在 Java 中，我们可以写这么一段代码 `int a = 3;`　然后我们发现 `a`  并没有封装任何的属性或者方法。

因此我们可以说 `a` 不是一个「对象」，Java 不是一门「纯粹面向对象」的语言。

再看看 Scala ，不论是低阶的 `Int`，`Double`，还是高阶类型，都封装有属性或者方法，因此 Scala 才是一门「纯粹面向对象」的语言。

那么是什么支持 Scala 一切皆为「对象」的呢？－**Scala 的通用类型系统。**

## Scala 通用类型系统
### 顶类型
我们知道，在 Java 中，所有「对象」的「顶类型」都是 `java.lang.Object`，但是 Java 却忽略了 `int`，`double`等 JVM 「原始类型」,它们并没有继承 `java.lang.Object`。

但是在 Scala 中，存在一个通用的「顶类型」－　Any。

![](/image/java2Scala-1-oop/scala-unified-types.png)

Scala 引入了`Any` 作为所有类型共同的顶类型。`Any` 是 `AnyRef` 和 `AnyVal` 的超类。

`AnyRef` 面向 Java（JVM）的对象世界，它对应 `java.lang.Object` ，是所有对象的超类。

`AnyVal` 则代表了 Java 的值世界，例如 `int` 以及其它 JVM 原始类型。

正是依赖这种继承设计，我们才能够使用 `Any` 定义方法，同时兼容 `scala.int` 以及 `java.lang.String` 的实例。

```Scala
class Person

val allThings = ArrayBuffer[Any]()

val myInt = 42             // Int, kept as low-level `int` during runtime

allThings += myInt         // Int (extends AnyVal)

allThings += new Person()  // Person (extends AnyRef), no magic here
```

正是通过这种「通用类型系统」的设计，使得 Scala 摆脱「原始类型」这种边缘情况的纠缠，从而实现「纯粹的面向对象」。

说完了「顶类型」，我们再来看看「底类型」。

### 底类型
我们知道在 Java 中比较闹心的就是异常处理，当我们调用一个抛出异常的方法，我们必须抛出或者处理异常。

但是在 Scala 中，我们知道一切表达式皆有类型，难道「抛异常」也是有类型的？

```Scala
scala> val a = Try(throw new Exception("123"))
a: scala.util.Try[Nothing] = Failure(java.lang.Exception: 123)
```
我们发现「抛异常」竟然是 `Nothing` 类型，在 Scala 中，难道 `Nothing` 仅仅是作为「抛异常」的类型？

```Scala
scala>  def fun(flag:Boolean)={
          if(flag){
            1                          // Int
          }else{
           throw new Exception("123") //Nothing
          }
        }
fun: (flag: Boolean)Int

```
我们发现 `fun` 函数并没有报错，而且返回值类型竟然是 `Int`，这让我们有一个大胆的猜测：**`Nothing` 是 `Int` 的子类型。**

```Scala
[Int] -> ... -> AnyVal -> Any
Nothing -> [Int] -> ... -> AnyVal -> Any
```
其实在 Scala 中， `Nothing` 不仅仅是 `Int` 的子类型，它更是**所有类型的子类型。** 这让我们又产生了一个大胆的猜测：难道 `Nothing` 继承了所有的类型？咳咳，这个问题我们以后在讨论。

在 Scala 中，还有一个类型 `Null` 遵循着和 `Nothing` 一样的原理。

```Scala
scala> def fun2(flag:Boolean)={
          if(flag){
            "123"  //String
          }else{
            null   //Null
          }
        }
fun2: (flag: Boolean)String
```
同理，我们可以得出 `Null` 是 `String的子类型`
```Scala
[String] -> AnyRef -> Any
Null -> [String] -> AnyRef -> Any
```
那我们看看 `Null` 是否可以兼容 `Int`。
```
scala>  def fun3(flag:Boolean)={
          if(flag){
            123  //Int
          }else{
            null   //Null
          }
        }
fun3: (flag: Boolean)Any
```
我们发现 `fun3` 的返回值类型竟然是 `Any`，说明 `Null` 不能兼容 Scala 的「值类型」，其实从 Scala 的帮助手册中我们就可以得出结论：**`Null` 是所有引用类型的子类型**

```Scala
abstract final class Null extends AnyRef
```
正因如此，`fun3` 的返回值类型才是 `Any`，因为 `Any` 才是 `AnyVal` 和 `AnyRef` 公共的超类。

「通用类型系统」我们先介绍到这里，下面我们小结一下。
## 小结

1.模板是封装属性或者方法的一种抽象。
2.对象是具有属性或者方法的一种实体。
3.封装的方式有三种：基于对象、基于类、基于原型。
4.「面向对象」是一种设计程序的方式，它要求工程师以**对象**为最小单位设计程序。
5.「纯粹面向对象」要求一切变量皆为对象，Scala 是，Java 不是。
6.Scala 的「顶类型」是 `Any`，它有两个直接的子类型：`AnyRef` 面向 JVM 对象的世界，`AnyVal` 面向 JVM 值的世界。
7.Scala 有两个「底类型 	」，`Nothing` 所有类型的底类型，`Null` 所有引用类型的底类型。
6.「通用类型系统」的设计使得 Scala 摆脱 JVM 原始类型的纠缠，从而实现「纯粹面向对象」。

本文以面向对象为引子，找到了一个 Java 和 Scala 共有的知识节点，从而引出 Scala 的通用类型系统，希望大家继续关注我的下一篇文章。

>[Scala 类型的类型（一）。](https://scala.cool/2017/03/scala-types-of-types-part-1/)
