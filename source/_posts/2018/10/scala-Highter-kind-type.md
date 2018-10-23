---
title: Scala 高等类型——你就是你
date: 2018/10/22
categories: 编程语言
tags:
- Scala
- 知原理
---

# Java List 的一个设计缺陷
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
其实上面的代码在设计上就有一点小问题：
* 对于`ArrayList[A]`来说，它的`subList`方法返回的竟然是`List[A]`类型。 这个让人感觉到有点诧异，一个对象的`subValue`难道不应该是自身类型吗？ 就像`String`对象调用`substring(b,e)`方法返回的应该是`String`对象本身的类型。
* 强制转换`List[A]`类型为`ArrayList[A]`导致运行期抛出异常。
