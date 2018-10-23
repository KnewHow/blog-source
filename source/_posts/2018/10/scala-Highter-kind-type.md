---
title: Scala 高等类型——二维泛型
date: 2018/10/23
categories: 编程语言
tags:
- Scala
- 知原理
---

# Java ArrayList&lt;T&gt; 的一个设计缺陷
在 Java 中，`ArrayList`是我们常用的一个容器，它有一个方法，`subList(int fromIndex, int toIndex)` 可以获取到一个该 list 的一个子序列。我们可以使用如下的代码来测试它：
```Java
@Test
public void subListSpec() {
    ArrayList<Integer> a = new ArrayList<Integer>();
    a.add(1);
    a.add(2);
    ArrayList<Integer> r = (ArrayList<Integer>) a.subList(0, 1);
    assertEquals(r, new ArrayList<Integer>().add(2));
}
```
然后我们运行上述代码，竟然会报错了(JDK 版本为1.8，操作系统为 Ubuntu 16.04):
```Java
java.lang.ClassCastException: java.util.ArrayList$SubList cannot be cast to java.util.ArrayList
```
其实上面的代码在设计层面就有一点小问题：
<!-- more -->
* 对于`ArrayList<A>`来说，它的`subList`方法返回的竟然是`List<A>`类型。 这个让人感觉到有点诧异，一个对象的`subValue`难道不应该是自身类型吗？ 就像`String`对象调用`substring(b,e)`方法返回的应该是`String`对象本身的类型。

让我们把上面的问题放大一些，当我们在设计一个接口的时候，我们该如何确定返回值类型呢？让我们来设计一个简单的容器：`Container`，如果按照Java的编程思想，你可能会这么设计接口：
```Java
public interface Container<T> {
    public Container<T> put(T t);

    public T get(Container<T> c);
}
```
而子类，或许你会这么设计：
```Java
public class MyList2<T> implements Container<T> {

    public Container<T> put(T t) {
        return null;
    }

    public T get(Container<T> c) {
        return null;
    }

}
```
上面的代码看上去很不错，但是它有一个问题：`MyList2`的`put`方法返回值竟然是`Container`类型，这会给使用者造成很大的疑惑，甚至有时候会造成类型转换异常。

# 一个更合理的设计方案
产生上面问题的原因是我们在父类(接口)中过早的定义了方法的返回值类型，当子类去实现的时候，使用父类的返回类型作为返回类型，而这种实现方式在某些场景下是不合理的。

我们来尝试一种理论上更合理的实现方式，先不考虑使用哪种语言来实现。

既然错误的原因是由于父类过早的写死了返回值的类型，那我们能不能传递一个子类类型的**泛型**给父类，当不同的子类实现的时候，返回值会根据子类传递给父类的泛型来确定。

此时这个泛型已经不仅仅是一个简单的泛型`T`，它是一个表示泛型类的泛型，例如它可以表示`List<T>`,我们暂时使用`[_]`来表示这个泛型，那上面`Container`的例子就可以进入如下的改写：
```Java
// 这里的M可以看做是一个泛型的变量，它代表一个泛型类的类型，例如List[A]
public interface Container<M [_]> {
    // 这是一个泛型方法，其中A是泛型，返回值是 M[A] 代表着某个泛型类，如List[A]
    public <A> M[A] put(A t);
    // 这是一个泛型方法，其中 A 是泛型，参数是 M[A] 代表着某个泛型类，如List[A]
    public <A> A get(M[A] c);
```
那么子类就可以很轻松的实现：
```Java
// 把 MyList2[A] 作为泛型传递给父类
public class MyList2<A> implements Container<MyList2> {
    // 此时返回值类型理所应当就是List[A]
    public <A> MyList2<A> put(A t) {
        return null;
    }
    // 此时参数类型理所应当就是List[A]
    public <A> A get(MyList2<A> c) {
        return null;
    }
```

通过这种把子类当做泛型传递给父类的操作，我们就可以在定义父类(接口)的时候，灵活的使用参数和返回类型，不幸的是，Java似乎不支持这种语法！

但是Scala支持这种语法，而且这种特性在Scala中被称作高等类型(Higher-kinded types)

# 使用 Scala 高等类型来完成 Container
```Scala
import org.scalatest.FlatSpec

class ScalaHigherKindedSpec extends FlatSpec {
  // 定义一个Container trait，它拥有一个高等类型
  trait Container[M[_]] {
    def put[A](a: A): M[A]
    def get[A](a: M[A]): A
  }

  // 把 List[A] 作为类型传递给 Container
  case object MyList extends Container[List] {
    def put[A](a: A): List[A] = List(a)
    def get[A](a: List[A]): A = a.head
  }

  // 把 Some[A] 作为类型传递给 Container
  case object MySome extends Container[Some] {
    def put[A](a: A): Some[A] = Some(a)
    def get[A](a: Some[A]): A = a.get
  }

  // 测试
  "test MyList" should "succeed" in {
    val ml = MyList.put(1)
    assert(MyList.get(ml) == 1)
  }

  "test MySome" should "succeed" in {
    val ms = MySome.put(2)
    assert(MySome.get(ms) == 2)
  }
}
```

# 小结
到此，我们可以发现Scala的高等类型是一种比泛型更加抽象的泛型，我们可以把它理解为二维的泛型，它表示的是一个带泛型的类型参数，定义为`trait Container[M[_]]`，如果你希望子类可以进行逆变或者协变，你可以使用这样的定义：
`trait Container[M[+ _]]`
`trait Container[M[- _]]`
