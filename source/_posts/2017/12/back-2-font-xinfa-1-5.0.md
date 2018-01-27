
---
title: 后端工程师入门前端页面重构（二）：心法 I
date: 2018/01/05
categories: 前端页面重构系列
tags:
- 前端
---

上一篇博客是我们《后端工程师入门前端页面重构》系列的第一篇，我们介绍了页面布局的口诀：

**从左到右，从上到下，化整为零。**

那么在接下来的几篇文章中，我们就来聊聊页面布局的「心法」和一些具体的「招式」。

那么什么是心法呢？

<!-- more -->

如果说口诀是页面布局的原则，那么心法就是对页面布局中一些重要概念的认识。

在上一篇文章，我们一直推荐使用高效的浮动布局，类似大家都玩过的磁铁，在磁铁的周围，所有的铁块会被磁铁所吸引。

那么浮动就好比页面上的一块磁铁，它会吸引页面上的元素块，让它们朝一个方向进行组合、包含、交叠，进而完成整个页面的布局。

下面就让我们来看看页面中元素有什么类型。

## HTML 块状元素和行内元素

在我们熟知的页面布局中，网页的标题，logo等都是有高度和宽度的。我们来看下面的豆瓣首页的切图：

![豆瓣首页切图](/image/back-2-font-xinfa-1/douban-index.png)

在上面的图片中，我们使用红色的线条和文字标注豆瓣 logo 的高度和宽度。不仅仅是 logo 图有高度和宽度，搜索框，热搜主题等其它元素，几乎都有高度和宽度。

那么在 HTML 语言中，我们如何指定元素的高度和宽度呢？

我们先来写一段 HTML：

```html
<div>这是 div 标签里面的内容</div>
<span>这是 span 标签的内容</span>
```

然后给它们定义样式：

```css
div {
  background-color: red;
  height: 100px;
  width: 100px;
}
span {
  background-color: green;
  height: 100px;
  width: 100px;
```

效果如下：

![效果图](/image/back-2-font-xinfa-1/html-css-show.png)

发现一个问题：

**我们设置的高度和宽度只对 div 标签产生效果，对 span 标签没有产生效果。这是为什么呢？**


其实在 HTML 中，我们可以把标签分为「块状元素」和「行内元素」，上面代码中的 div 标签就是块状元素，而 span 标签就是行内元素。

那么这两类元素有什么区别呢？

从我们的代码的效果图里面我们已经看出来一个区别了：**块状元素可以设置它的高度和宽度，行内元素对的高度和宽度的设置是无效。**

我们把上面元素的宽度和高度都删除，让 div 标签和 span 标签保持原始的高度和宽度来看看效果。

![效果图](/image/back-2-font-xinfa-1/block-inline-elemet.png)

是不是又看出来一个区别呢？

**块状元素是独占一行的，而行内元素只占本身内容的大小。**

看看我们之前设置高度和宽度的例子，我们用浏览器检查一下看看。

![效果图](/image/back-2-font-xinfa-1/block-element.jpg)

**即使我们设置了块状元素的高度和宽度，它还是独占一行的**。真的是霸道啊！

然而，现实中的网页（如豆瓣），很多内容块都是拼接的，如果我们使用块状元素来表示这些内容块，如果消除它独占一行的情况呢？


关于这个问题，似乎有两种解决方案。


### inline-block

其实在 css 的 diplay 属性中，有一个属性值 `inline-block` 可以将标签呈现出「块状元素」和「行内元素」之间的中间态，即它可以拥有宽高度的同时，也可以具备行内元素的占位属性。

```html
<div>123</div>
<div>123</div>
```

然后给它们定义样式：

```css
div{
    background-color: red;
    display: inline-block;
}
```

看效果：
![行内元素误差效果显示](/image/back-2-font-xinfa-1/inline-element-error.png)

我们发现**虽然两个 div 可以变成行内元素在一行显示，但是它们之间还是存在空白，不能完美的相邻在一起。这点空白会让我们的布局很不美观！**

### 浮动
上面我们说了，浮动可以把页面上的元素往某一个方向吸引，那么如何吸引呢？
在 CSS 中，我们可以通过 `float:left` 把元素往左边吸引

```html
<div style="background-color: red">这是第一个区块</div>
<div style="background-color: green">这是第二个区块</div>
```

然后给它们定义样式：

```css
div{
    width: 200px;
    height: 200px;
    float: left;
}
```
看效果：

![简单浮动效果图](/image/back-2-font-xinfa-1/float-layout-show-1.png)

我们发现，通过浮动，可以使两个原本很难相邻在一起的块状元素，**完美** 的相邻在一起。

`left` 是把元素往左边吸引，而`right` 是把元素往右边吸引。


## 父元素高度问题
在实际开发中，我们经常需要标签的嵌套使用，下面我们来模拟一下场景，代码如下：
{% codeblock lang:html %}
	<style type="text/css">
	    .block-1{
		width: 200px;
		height: 200px;
		float: left;
		background: red;
	    }

	    .block-2{
		width: 200px;
		height: 200px;
		float: left;
		background: green;
	    }
	</style>

	<div class="parent">
	    <div class="block-1">
		我是第一个子元素
	    </div>

	    <div class="block-2">
		我是第二个子元素
	    </div>
	</div>
{% endcodeblock %}


这段代码有问题吗？　当然没问题啦，不信我们使用谷歌浏览器检查一下。

![](/image/back-2-font-xinfa-1/second-check-child.png)

子元素宽度和高度都是 OK 的。

我们再来看看它父元素高度和宽度。

![](/image/back-2-font-xinfa-1/parent-check.png)

我去！父元素的高度竟然是 **０**！！　我是不是安装了假的浏览器？


## 清除浮动
还是嵌套的使用元素标签，代码如下：
{% codeblock lang:html %}
        <style type="text/css">
            .first{
                background: red;
                width: 200px;
                height: 200px;
                float: left;
            }
            .second{
                background: yellow;
                width: 200px;
                float: left;
                height: 200px;
    
            }
            .third{
                float: left;
                background: green;
                width: 200px;
                height: 200px;
            }
        </style>

        <div class="parent">
            <div class="first">
                第一个区块
            </div>
    
            <div class="second">
                第二个区块
            </div>
    
            <div class="third">
                第三个区块
            </div>
        </div>
{% endcodeblock %}

不用看效果图我们都知道：**子元素高度正常，父元素高度为0。**
现在我们有一个需求：在保持浮动的情况下，让第三块区域在下一行显示，如何去做？

这就要使用到另个招式： 「清除浮动」。

我们只要在第三块区块的「.third」中添加
{% codeblock lang:html %}
    clear: left;
{% endcodeblock %} 

即可。

话不多说，让我们先看效果。

![添加 clear: left;的效果图](/image/back-2-font-xinfa-1/add-clear-left-1.png)

真的把第三个区块给压下来了！



还是按照上面的套路，我们来分析「 clear: left; 」这段代码：

它用到的关键字是「 clear 」，它的含义是清除。

而 「 left 」 代表是左边。

那么它合起来的意思是：**清除左边的浮动元素**。

说到更明白一点就是：**不让当前元素的左边有浮动元素。**

当前元素是第三个区块，它的左边有两个浮动元素 first 和second 。

那这段代码的意思就是：**不让第三个区块左边有浮动元素**，那浏览器这么处理呢？

浏览器会说：“老三啊，你的左边有两个浮动元素，但是你又不想左边有浮动元素，所以你就吃点亏，到 **下一行** 来吧！”

于是第三个区块就到了下一行啦。

虽然有点复杂，但是按照上面思路进行一步步分解，是不是就变的很简单呢？

让我们再来拓展一下，clear 除了可以设置为 left，还可以设置为 「 right 」 和 「 both 」。

我相信不用我说你们也明白了吧！

right 就是不让当前元素 **右边** 存在浮动元素嘛。

那 both 就是不让当前元素的 **两边** 出现浮动元素喽。

说了这么多，我们好像还有一个问题没有解决，父元素的高度还是0啊！

这个有点尴尬！

## 父元素高度真的需要吗？

那么，在解决之前，按照我们的套路，我们要问一个问题：我们可以不管父元素的高度吗？反正布局都设计出来了。

我的实习导师告诉我：“这是不行的。**浮动布局的占位空间往往是我们理想的父元素高度。**”

他说的太抽象，我们来说的直白一点：

「在布局中我们往往使用 **浮动布局** 来实现某一块区域的布局」。

「然后我们最好用一个DIV去 **包裹** 整个浮动布局，用于和其它的布局区分开」。

「最后我们要求这个父DIV和高度要和浮动布局的 **高度** 一样」。

对于上面的三点内容，前两点在我们的代码中已经做到了。

想要解决第三个问题，就得使用我们上面讲的招式- **清除浮动**。

我们在父区块最后面增加一个空的DIV，将它设置它为「 clear:both 」。

为什么要这么使用呢，具体的原因我也讲不清楚，大概是这样的吧：

因为浮动元素会影响它的位置，我们必须确保它在父元素的最后一行，不受 **任何** 浮动元素干扰,我们必须清除浮动对它的干扰。

来来来，代码走一波！

{% codeblock lang:html %}
        <style type="text/css">
            .first{
                background: red;
                width: 200px;
                height: 200px;
                float: left;
            }
            .second{
                background: yellow;
                width: 200px;
                float: left;
                height: 200px;
    
            }
            .third{
                float: left;
                background: green;
                width: 200px;
                height: 200px;
                clear: left;
            }
            .last{
                clear: both;
            }
        </style>

        <div class="parent">
            <div class="first">
                第一个区块
            </div>
    
            <div class="second">
                第二个区块
            </div>
    
            <div class="third">
                第三个区块
            </div>
            <div class="last">
    
            </div>
        </div>
{% endcodeblock %}

然后我们会发现，父元素竟然有高度了，而且和浮动布局的高度是一样的！

![父元素有高度了](/image/back-2-font-xinfa-1/parrent-height.png)

哈哈，到了这里，我们已经完全解决父元素高度为 0 的问题，那么我想问：

上面的解决方法是最好的吗？


## 清除浮动－最佳实践

按照套路，我们得问一个问题：上面的解决方案有问题吗？

回答是肯定的！

因为按照上面的方法，我们需要在 **手动** 在每个父区块最后添加一个空的 DIV，这样的做法是 **低效的**！

因为一个页面有成百上千个布局区块，如果每一个我们都手动写一个空的　DIV，增加工作量不说，日后维护起来，也很麻烦。

我们可不可以在某个地方统一定义，然后全局使用呢？

回答是当然可以啦。

这个要使用到另一个新的招式-CSS 的「伪类」,伪类的具体用法可以参考[W3C的CSS伪类教程。](http://www.w3school.com.cn/css/css_pseudo_elements.asp)

我们这里使用的是「after」伪类，它可以自动的在某个父元素的最后一行添加最后一个子元素，并且设置属性。

上面的话有点拗口，来来来，我们直接上代码分析：

{% codeblock lang:html %}
    .parent:after{
        /*设置最后一个元素的内容为空*/
        content: "";
        /*设置最后一个元素为清除两边浮动*/
        clear: both;
        /*设置最后一个元素为块状元素*/
        display: block;
    }

{% endcodeblock %}

上面的代码会自动在「parent」作用的标签的最后一行添加一个子元素，并且设置该元素为「块状元素」「内容为空」以及「清除浮动」。

只要把父元素的 class属性设置值为 「parent」，那么就可以直接使用啦。

来来来，让我们完整的敲一次代码！

{% codeblock lang:html %}
        <style type="text/css">
            .first{
                background: red;
                width: 200px;
                height: 200px;
                float: left;
            }
            .second{
                background: yellow;
                width: 200px;
                float: left;
                height: 200px;
    
            }
            .third{
                float: left;
                background: green;
                width: 200px;
                height: 200px;
                clear: left;
            }
    
            /*
              伪类
             */
            .parent:after{
                /*设置最后一个元素的内容为空*/
                content: "";
                /*设置最后一个元素为清除两边浮动*/
                clear: both;
                /*设置最后一个元素为块状元素*/
                display: block;
            }
        </style>

        <div class="parent">
            <div class="first">
                第一个区块
            </div>
    
            <div class="second">
                第二个区块
            </div>
    
            <div class="third">
                第三个区块
            </div>
    
        </div>
{% endcodeblock %}

![使用最佳实践-伪类后的效果图](/image/back-2-font-xinfa-1/div-float-after.png)

## 心法小结


最后，我们再来回顾一下文章介绍的一些心法：

**1. HTML 分为块状元素和行内元素的，块状元素是独占一行的**

**2. 浮动布局是解决多个块状元素在同一行的最佳方法**

**3. 在嵌套的标签中使用浮动时，如果不使用清除浮动，父元素的高度会为0**

**4. 清除浮动 + after伪类 是浮动布局的一个最佳实践**

文章中的一些招式可能说的太粗糙，先不用捉急，我们先学心法，招式到后面再专门的学习。

我会在下一篇博文继续介绍心法 II。

