---
title: 用Kotlin创建UI
date: 2018/08/19
categories: 编程语言
author: How
tags:
- Kotlin
---
# 第十六章 用Kotlin创建UI
众所周知，Kotlin一般用于在安卓端的布局，其实它也可以用来实现WEB端的布局。那么为什么Kotlin可以实现在不同平台的UI布局呢？一个很重要的原因就是Kotlin天生支持DSL，我们将在第一小节来介绍它们。在第二小节我们将介绍如何使用Kotlin来实现WEB端的UI布局，在第三小节我们将介绍如何使用Kotlin实现安卓端的UI布局。在UI实战前，让我们先来看看Kotlin是如何支持DSL的吧。

## 16.1 DSL 和 Type-Safe Builders
在Kotlin中天然支持DSL，而Type-Safe Builders 就是为了DSL服务的，在正式讲Type-Safe Builders之前，我们先来了解一下什么是DSL。

### 16.1.1 DSL
DSL其实是Domain Specific Language 的缩写，中文翻译为领域特定语言。与其对立的为GPL，这里的GPL可不是GPL协议，而是General Purpose Language的简称，即通用编程语言，我们非常熟悉的Java，C，C++都可以算作GPL的范畴。正如DSL中文翻译的那样，DSL所擅长的是`特定的领域`，与GPL相比，它们可以不是图灵完备的，只是为解决特定的领域而产生的某个语言。

在我们经常使用的编程语言中，SQL可以被看做是一个DSL，因为它仅仅只用于操作数据库，并不像Java、C++那样可以编写任何的计算机程序。按照这样分析，DSL岂不是不够强大。从一定程度上来说确实如此，可以DSL就是为了解决专用领域而产生的，定位就是解决专用领域的问题，不需要那么强大。而且DSL很容易学会，思考你在学习Java花的时间和学习SQL花的时间，就能明白DSL比传统的GPL是有多容易上手。

DSL经过这么多年的发展，也逐渐分为了两种形式：
* 外部的DSL
* 内部的DSL

像上面提到的SQL，它就是一种外部的DSL，因为它是完全的独立的。即使你在Java中使用SQL语句，也是独立的编写SQL语句。但是还有一种DSL可以依赖宿主语言的特性来完成DSL的使用，只是在编译时期把它转换为特定领域语言。在Scala中有一个著名的ORM框架为Quill，它就是使用内部的DSL来完成的。
如下面的一段代码：
```Scala
val q = quote {
  query[Person].filter(p => p.age > 18).flatMap(p => query[Contact].filter(c => c.personId == p.id))
}

ctx.run(q)
```
在编译时期会把上面的代码转换为：
```SQL
SELECT c.personId, c.phone FROM Person p, Contact c WHERE (p.age > 18) AND (c.personId = p.id)
```

Quill完全利用了Scala的语言特性，使用`filter`函数来实现SQL语言的`where`条件，使用`flatMap`函数来实现多表查询。这种内部的DSL给使用的者的感觉就是我完全在使用Scala编程，根本不需要学习如何写SQL，而且代码的可维护性和健壮性得到了很大的提高。在编写原生的SQL时，很可能会出现语法的错误，而这些错误，往往运行时期才会显露出来。而使用内部的DSL，由于依赖宿主语言的语法特性，很多错误在编译时期就可以被编译器检测出来。上文中我们提到，Kotlin天生支持DSL，当然这里的DSL指的是内部的DSL。我们先来看一段代码：
```Kotlin
fun main(args: Array<String>) {
    val result =
            html {
                head {
                    title { +"HTML encoding with Kotlin" }
                }
                body {
                    h1 { +"HTML encoding with Kotlin" }
                    p { +"this format can be used as an alternative markup to HTML" }

                    // an element with attributes and text content
                    a(href = "http://jetbrains.com/kotlin") { +"Kotlin" }

                    // mixed content
                    p {
                        +"This is some"
                        b { +"mixed" }
                        +"text. For more see the"
                        a(href = "http://jetbrains.com/kotlin") { +"Kotlin" }
                        +"project"
                    }
                    p { +"some text" }

                    // content generated from command-line arguments
                    p {
                        +"Command line arguments were:"
                        ul {
                            for (arg in args)
                                li { +arg }
                        }
                    }
                }
            }
    println(result)
}
...
```
这是来自Kotlin官方的一段关于DSL代码，这里只给出了部分，完整的代码链接：https://try.kotlinlang.org/#/Examples/Longer%20examples/HTML%20Builder/HTML%20Builder.kt

乍一看，这好像是HTML的代码，但是事实上这是完全的Kotlin代码，你完全可以编译运行它，而且它会为你生成对于的HTML代码。这就是我们一直所说的Kotlin天生支持DSL，那Kotlin是如何天生支持DSL的呢？它凭什么让`html`标签中只包含`head`标签和`body`标签，而且不是其它任意种类的标签呢？其实提供这个保证的正是Type-Safe Builders。

### 16.1.2 Type-Safe Builders
我们先来看一段上述代码的一些实现的逻辑，这同样是来自Kotlin官方给的演示代码：
```Kotlin
interface Element {
    fun render(builder: StringBuilder, indent: String)
}

class TextElement(val text: String) : Element {
    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent$text\n")
    }
}

abstract class Tag(val name: String) : Element {
    val children = arrayListOf<Element>()
    val attributes = hashMapOf<String, String>()

    protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
        tag.init()
        children.add(tag)
        return tag
    }

    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent<$name${renderAttributes()}>\n")
        for (c in children) {
            c.render(builder, indent + "  ")
        }
        builder.append("$indent</$name>\n")
    }

    private fun renderAttributes(): String? {
        val builder = StringBuilder()
        for (a in attributes.keys) {
            builder.append(" $a=\"${attributes[a]}\"")
    }
        return builder.toString()
    }


    override fun toString(): String {
        val builder = StringBuilder()
        render(builder, "")
        return builder.toString()
    }
}

abstract class TagWithText(name: String) : Tag(name) {
    operator fun String.unaryPlus() {
        children.add(TextElement(this))
    }
}

class HTML() : TagWithText("html") {
    fun head(init: Head.() -> Unit) = initTag(Head(), init)

    fun body(init: Body.() -> Unit) = initTag(Body(), init)
}

class Head() : TagWithText("head") {
    fun title(init: Title.() -> Unit) = initTag(Title(), init)
}
```
在上面的代码中，在`HTML`类中，只有`head`和`body`方法，这就意味着，你只能在HTML标签中直接写入`head`标签和`body`标签，如果你写入其它的标签，那么编译器会给你报一个错误。这正是Type-Safe Builders的价值所在，在语法上可以尽可能的贴近HTML语言的同时，又不缺乏安全性。到这里，你可能对Type-Safe Builders有了一定的理解，但是对为什么Kotlin可以使用下面这样的语法而迷惑不解：
```Kotlin
html{
  body{...}
}
```

其实Kotlin可以实现上面形如HTML的代码的原因在于你可以在函数参数中使用——带有接收者的Lambda。带接受者的Lambda在第二章已经介绍过了，这里就不进行多余的介绍。

## 16.2 使用Kotlin实现WEB端UI设计
在上一个小节中我们介绍Kotlin的DSL，在这节中我们讲介绍如何使用Kotlin来布局一个简单的WEB端的UI。我们知道，WEB端的UI主要由HTML、CSS和JavaScript组成，那么Kotlin是如何分别扮演这三者的角色的呢？这里我们使用`kotlin-stdlib-js`工具包来实现具体代码的编译和转换。

### 16.2.1 使用HTMLElement表示HTML元素
在WEB端，页面的骨架主要是由HTML的各种元素组成的，在原生的Kotlin中，并不存在这些元素，幸运的是工具已经帮我们封装好了这些元素。例如你可以使用`HTMLDivElement`来表示一个div元素，使用`HTMLInputElement`来表示一个`input`元素。几乎每个HTML元素都有对应的接口，如果你想写一个登录框，那么你可以使用如下的Kotlin代码：
```Kotlin
val form = document.createElement("form") as HTMLFormElement
    form.id = "loginform"

    val message = document.createElement("span") as HTMLSpanElement
    message.id = "message"

    val username = document.createElement("input") as HTMLInputElement
    username.type = "text"
    username.placeholder = "请输入用户名"
    username.id = "username"

    val password = document.createElement("input") as HTMLInputElement
    password.type = "password"
    password.placeholder = "请输入密码"
    password.id = "password"
    password.style.marginTop="10px"

    val submit = document.createElement("button") as HTMLButtonElement
    submit.type = "submit"
    submit.textContent = "登录"
    submit.style.background="green"
    submit.style.marginTop="10px"

    form.append(message, lineBreak(), username, lineBreak(), password, lineBreak(), submit)
    form.addEventListener("submit", listener)
    return form
}
```

其实这段代码很像我们直接在JavaScript中动态的添加DOM元素，使用`document.createElement`来创建元素，并且最终把它们添加到父元素`form`中。不过上述的代码是类型安全的，因为每个HTML标签对于各自的Kotlin接口，而且在编译时期会进行类型检查。

### 16.2.2 使用ElementCSSInlineStyle设计样式
细心的同学在上面的代码已经发现了一些端倪，我们使用`Element.style`来为元素添加样式，而且样式的名称和CSS中的样式名称几乎一模一样。其实`.style`方法获取了一个`CSSStyleDeclaration`的抽象类，而它里面有很多对应的CSS的样式属性，下面给出`CSSStyleDeclaration`部分属性代码：
```Kotlin
public external abstract class CSSStyleDeclaration : ItemArrayLike<String> {
    open var cssText: String
    override val length: Int
    open val parentRule: CSSRule?
    open var cssFloat: String
    open var _dashed_attribute: String
    open var _camel_cased_attribute: String
    open var _webkit_cased_attribute: String
    open var alignContent: String
    open var alignItems: String
    open var alignSelf: String
    open var animation: String
    open var animationDelay: String
    open var animationDirection: String
    open var animationDuration: String
    open var animationFillMode: String
    open var animationIterationCount: String
    open var animationName: String
    open var animationPlayState: String
    open var animationTimingFunction: String
    open var backfaceVisibility: String
    open var background: String
    open var backgroundAttachment: String
    open var backgroundClip: String
    ...
}
```
同样的这些样式都是类型安全的，编译时期会做类型检查，没有定义的样式是不能设定的。

### 16.2.3 使用EventListener表示JavaScript的各种事件
在WEB端布局中，JavaScript可以为元素绑定各种各样的事件，同样的在Kotlin WEB UI中我们可以使用`EventListener`来绑定各种事件。例如在16.2.1节中我们使用`form.addEventListener("submit", listener)`来为`submit`元素绑定一个事件，而事件的定义代码如下：
```Kotlin
import org.w3c.dom.*
import org.w3c.dom.events.EventListener
import kotlin.dom.appendText
import kotlin.dom.clear

fun processLogin(div: HTMLDivElement, form: HTMLFormElement) {
    val username = form.children["username"] as HTMLInputElement
    val password = form.children["password"] as HTMLInputElement
    if(username.value == "How" && password.value == "How") {
        div.clear()
        div.appendChild(welcomeView(logoutAction(div)))
    } else {
        val message = form.children["message"] as HTMLSpanElement
        message.clear()
        message.appendText("非法的用户名和密码")
        message.style.color = "red"
    }
}

fun loginAction(div: HTMLDivElement) = EventListener {
    it.preventDefault()
    processLogin(div, it.target as HTMLFormElement)
}
```
通过上述的代码我们可以发现，Kotlin中的`EventListener`和JavaScript操作DOM元素一样强大。除此之外`EventListener`更是类型安全的，而且它比原生JavaScript可以更方面的处理DOM，而且最终通过编译完全的转换为JavaScript代码。

到此我们介绍了如何使用Kotlin的`kotlin-stdlib-js`类库来进行WEB端的UI设计，章节中只给出了部分演示代码，完整的代码可以参考：https://github.com/KnewHow/kotlin-web-ui


## 16.3 Kotlin 来构建UI
Kotlin在Android生态圈中可谓是大放异彩的存在，那么它比传统的XML的布局方式有什么优势呢？让我们先来了解一下传统的XML的布局方式。

### 16.3.1 原生的XML布局
在原生的Android，你可以使用XML的方式来进行布局，Android提供了对应于View类及其子类的简明XML词汇，如用于小部件和布局的词汇。在 XML 中声明 UI 的优点在于，可以更好地将应用的外观与控制应用行为的代码隔离，这意味着你在修改或调整描述时无需修改源代码并重新编译。(引用自安卓开发者平台)。如果你想垂直布局一个`TextView`和`Button`,那么你可以使用如下的XML代码进行布局：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical" >
    <TextView android:id="@+id/text"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:text="Hello, I am a TextView" />
    <Button android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello, I am a Button" />
</LinearLayout>
```
在上述代码中，我们在`LinerLayout`标签内部引入了`TextView`和`Button`标签，并且使用`layout_height`和`layout_width`来设定器高度和宽度。这个有点类似我们网页端的布局方式，使用标签来指定特色的元素，并且设定它们的UI属性。

使用这种原生的XML的布局方式，对于新手来说是一个不错的练手方式，可是对于上述的代码，总让人感觉到一些混乱和复杂。就像WEB端布局一样，你只使用HTML+CSS+JavaScript就可以实现布局，可是这种布局需要写很多代码，而且模块化不是很好。那Android端有没有像Vue那样的框架给我们使用呢？幸运的是，Kotlin为我们提供了一个Anko框架。

### 16.3.2 Kotlin的Anko框架
Anko是Kotlin编写一款布局框架，它使用DSL来实现Android端的动态布局效果，例如在原生的Android布局中，你可以使用Java来编写一个如下的布局：
```Java
val act = this
val layout = LinearLayout(act)
layout.orientation = LinearLayout.VERTICAL
val name = EditText(act)
val button = Button(act)
button.text = "Say Hello"
button.setOnClickListener {
    Toast.makeText(act, "Hello, ${name.text}!", Toast.LENGTH_SHORT).show()
}
layout.addView(name)
layout.addView(button)
```
这段代码给人的感觉就是完全在写后端的代码，而且需要先定义属性，并且把属性设置给View，这种代码风格不仅仅混乱，而且可读性不高，下面我们尝试还有Anko框架来改写这段代码：
```Kotlin
verticalLayout {
    val name = editText()
    button("Say Hello") {
        onClick { toast("Hello, ${name.text}!") }
    }
}
```
是不是瞬间感觉整个世界都干净了，上面的一大片代码，使用短短几行的Kotlin代码就完成了！除此之外，Anko还具备很强的可扩展性。那我们如何在项目中使用Anko呢？我们需要在项目中添加如下依赖即可：
```Kotlin
dependencies {
    // Anko Layouts
    implementation "org.jetbrains.anko:anko-sdk25:$anko_version" // sdk15, sdk19, sdk21, sdk23 are also available
    implementation "org.jetbrains.anko:anko-appcompat-v7:$anko_version"

    // Coroutine listeners for Anko Layouts
    implementation "org.jetbrains.anko:anko-sdk25-coroutines:$anko_version"
    implementation "org.jetbrains.anko:anko-appcompat-v7-coroutines:$anko_version"
}
```
这里只是简单的介绍了一下Anko框架的使用，详细的用法可以参考：https://github.com/Kotlin/anko/wiki/Anko-Layouts

## 16.4 本章小结

- **DSL 和 Type-Safe Builders**
这是Kotlin的高阶特性，Kotlin使用这两种特性来编写工具包可以极大的简化原生代码的编写，而且不失语义性。

- ** 使用Kotlin实现WEB端UI设计**
使用Kotlin来实现WEB端的布局，来体会Kotlin DSL的强大，而且还介绍了使用Kotlin DSL的设计原理。

- ** 使用Kotlin实现Android端的布局**
对比了原生的XML布局和使用Kotlin Anko框架进行布局，发现Anko可以极大的简化布局方式，而且不失扩展性。
