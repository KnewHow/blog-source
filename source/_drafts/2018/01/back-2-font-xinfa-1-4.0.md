
---
title: 后端工程师入门前端页面重构（二）：心法 I　修改版－－易读性
date: 2018/01/05
categories: 前端页面重构系列
tags:
- 前端
---

上一篇博客是我们《后端工程师入门前端页面重构》系列的第一篇，我们介绍了页面布局的口诀：

**从左到右，从上到下，从整体到布局，化整为零。**

那么在接下来的几篇文章中，我们就来聊聊页面布局的心法。

如果说口诀可以让你学会布局一个简单的页面，那么心法会很大程度上提升你的布局效率。

在上一篇文章，我们一直推荐使用高效的浮动布局，那么这篇文章我们就先来看看什么是浮动布局吧。

## HTML 块状元素和行内元素

在我们熟知的页面布局中，网页的标题，logo等都是有高度和宽度的。我们来看下面的豆瓣首页的切图：

![豆瓣首页切图](/image/back-2-font-xinfa-1/douban-index.png)

在上面的图片中，我们使用红色的线条和文字标注豆瓣 logo 的高度和宽度。不仅仅的 logo 图有高度和宽度，搜索框，热搜主题等其它元素，几乎都有高度和宽度。

那么在 HTML 语言中，我们如何指定元素的高度和宽度呢？

首先我们来看一段简单的代码：

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
    	<title>div和span标签的使用</title>
    	<style type="text/css">
    		.div{
    			background-color: red;
    			height: 300px;
    			width: 300px;
    			/*选中我们的设置class="div"*/	
    		}
    
    		.element{
    			background-color: green;
    			height: 400px;
    			width: 400px;
    			/*选中我们的设置class="element"*/	
    		}
    	</style>
    </head>
    <body>
    	<div class="div">这是div标签里面的内容</div>
    	<span class="element">这是span标签的内容</span>
    </body>
    </html>
    
{% endcodeblock %}

在上面的代码中，我们使用 div 和 span 画了两个区域，并且使用「height」和「width」指定它们的高度。效果如下：

![效果图](/image/back-2-font-xinfa-1/html-css-show.jpg)

我们发现：我们设置的高度和宽度只对 div 标签产生效果，对 span 标签没有产生效果。这是为什么呢？

难道它们两是不同种类的元素？

嘿嘿，猜对啦！

其实在 HTML 中，我们可以把标签分为块状元素和行内元素，上面代码中的 div 标签就是块状元素，而 span 标签就是行内元素。

* 块状元素的标签还有：p、ul、ol等。

* 行内元素的标签还有：img、code、input等。

那么块状元素和行内元素有什么区别呢？

从我们的代码的效果图里面我们已经看出来一个区别了：**块状元素可以设置它的高度和宽度，行内元素对的高度和宽度的设置是无效。**

我们再来写一段代码试试，我们使用我们熟悉的 div 标签和 span 标签来演示。

我们把上面代码的高度和宽度注释掉，让 div 标签和 span 标签保持原始的高度和宽度来看看效果。

来来来，代码走一波！

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
    	<title>div和span标签的使用</title>
    	<style type="text/css">
    		.div{
    			background-color: red;
    			/*height: 300px;*/
    			/*width: 300px;*/
    			/*选中我们的设置class="div"*/	
    		}
    
    		.element{
    			background-color: green;
    			/*height: 400px;*/
    			/*width: 400px;*/
    			/*选中我们的设置class="element"*/	
    		}
    	</style>
    </head>
    <body>
    	<div class="div">这是div标签里面的内容</div>
    	<span class="element">这是span标签的内容</span>
    </body>
    </html>
{% endcodeblock %}

![效果图](/image/back-2-font-xinfa-1/block-inline-elemet.png)

是不是又看出来一个区别呢？

**块状元素是独占一行的，而行内元素只占本身内容的大小。**

看看我们之前设置高度和宽度的例子，我们用浏览器检查一下看看。

![效果图](/image/back-2-font-xinfa-1/block-element.jpg)

**即使我们设置了块状元素的高度和宽度，它还是独占一行的**。真的是霸道啊！

如果不会使用谷歌浏览器检查网页的同学，可以参考[如何使用谷歌浏览器检查页面。](https://jingyan.baidu.com/article/2f9b480db6cde741ca6cc246.html)


说了这么多，我好像还是没有教你们如何去写一个浮动布局。

咳咳，下面正式开始。


## 为什么要使用浮动布局

在开始之前，我们按照套路，还是要问一个问题：为什么要使用浮动布局，仅仅是因为它是高效的？

答案当然是否定的。

在上面的我们分析了块状元素是独占一行的，但是在页面布局的时候，往往都是要求许多块状元素是在一行的。

怎么解决这个问题呢？

解决问题有两种方案：
* 我们可以使用 CSS 把块状元素变成行内元素
* 我们使用浮动

对于第一种方案，我们直接否定！**因为它存在误差！**
下面我们使用 HTML 和 CSS 来写一段代码有误差的代码： 

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
    	<title>使用行内元素是有误差的</title>
    	<style type="text/css">
    		.div-1{
    			background-color: red;
    			/*让其作为行内元素显示*/
    			display: inline-block;
    			
    		}
    
    		.div-2{
    			background-color: green;
    			/*让其作为行内元素显示*/
    			display:inline-block;
    		}
    	</style>
    </head>
    <body>
    	<div class="div-1">123</div>
    	<div class="div-2">123</div>
    </body>
    </html>
{% endcodeblock %}

上面的这段代码，我们使用块状元素 div 写了两个区块，并且使用 
{% codeblock lang:html %}
    display:inline-block; 
{% endcodeblock %} 

让块状元素 div 来作为行内元素显示，然后我们来看效果：

![行内元素误差效果显示](/image/back-2-font-xinfa-1/inline-element-error.jpg)

我们发现**虽然两个 div 可以变成行内元素在一行显示，但是他们之间还是存在空白，不能完美的相邻在一起。**

这点空白会给我们布局带来很大的麻烦，这就是我们为什么选择浮动布局的原因。

选择浮动布局的原因知道了，下面我们来写一个浮动布局吧。

真的开始写喽，不是骗你们的！


## 一个简单的浮动布局

首先我们画两个 div，并且设置它们的背景颜色，高度以及宽度，代码如下：

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>简单的浮动布局</title>
        <style type="text/css">
            .block-1{
                width: 200px;
                height: 200px;
                background: red;
            }
    
            .block-2{
                width: 200px;
                height: 200px;
                background: green;
            }
        </style>
    </head>
    <body>
    
    <div class="block-1">
        这是第一个区块
    </div>
    
    <div class="block-2">
        这是第二个区块
    </div>
    </body>
    </html>
       
{% endcodeblock %} 

打开浏览器看效果是这个样子滴：

![浮动之前的效果图](/image/back-2-font-xinfa-1/float-layout.jpg)

这个效果很正常嘛。 div 作为很霸道的块状元素，当然占据一整行，两个 div 就分别占据两行喽。

下面我们就要使用浮动了，注意看清楚哦！

我们只要在上面代码中 div 的 class 选择器里面添加如下代码即可实现浮动。

{% codeblock lang:html %}
    float: left;
{% endcodeblock %}

来来来，完整代码走一波！

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>简单的浮动布局</title>
        <style type="text/css">
            .block-1{
                width: 200px;
                height: 200px;
                background: red;
                /*设置为浮动*/
                float: left;
            }
    
            .block-2{
                width: 200px;
                height: 200px;
                background: green;
                /*设置为浮动*/
                float: left;
            }
        </style>
    </head>
    <body>
    
    <div class="block-1">
        这是第一个区块
    </div>
    
    <div class="block-2">
        这是第二个区块
    </div>
    </body>
    </html>
{% endcodeblock %}

然后我们用浏览器打开看效果：

![简单浮动效果图](/image/back-2-font-xinfa-1/float-layout-show-1.png)

我们发现两个原本很难相邻在一起的块状元素，竟然完美的 **无缝** 的相邻在一起。

浮动的布局是不是很简单呢？我们只要添加 「 float: left;」即可实现浮动。你们学会了吗？

现在让我们重新来看一下「 float: left;」这段代码。

我们都知道「 float 」这个字是指定使用浮动的关键字，但是「 left 」是干嘛用的？难道还是指定 right？

当然可以啦。left 的意思是指定左浮动，意思是让元素悬浮在浏览器的左边，right就是相反的意思，让元素悬浮在浏览器的右边。

来来来，让我们把上面代码中的 left 变成right，代码再走一波!

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>简单的浮动布局</title>
        <style type="text/css">
            .block-1{
                width: 200px;
                height: 200px;
                background: red;
                /*设置为浮动*/
                float: right;
            }
    
            .block-2{
                width: 200px;
                height: 200px;
                background: green;
                /*设置为浮动*/
                float: right;
            }
        </style>
    </head>
    <body>
    
    <div class="block-1">
        这是第一个区块
    </div>
    
    <div class="block-2">
        这是第二个区块
    </div>
    </body>
    </html>
{% endcodeblock %}

让我们打开浏览器看效果！

![右浮动效果图](/image/back-2-font-xinfa-1/float-right.png)

是不是被浮动的结果吓了一跳呢？设置浮动的元素竟然都跑到右边去了。

到了这里，关于怎么写浮动，我想你们应该已经会了。下面我们来看一个问题，要跟着我的思路走哦。

还是回到我们之前设置的左浮动的页面。我们可以使用谷歌浏览器来检查一下它们的高度和宽度，和我们设置的完全一样。

这段代码没问题。

![](/image/back-2-font-xinfa-1/div-show-height.png)

## 还是一个简单的浮动布局
{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>还是一个简单的浮动布局</title>
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
    </head>
    <body>
        <div class="parent">
            <div class="block-1">
                我是第一个子元素
            </div>
    
            <div class="block-2">
                我是第二个子元素
            </div>
        </div>
    </body>
    </html>
    
{% endcodeblock %}

![效果图](/image/back-2-font-xinfa-1/float-layout-show-2.png)

这段代码还是很简单，我们还是使用　DIV　标签画了两个　200 * 200　区块，和之前代码的唯一区别在于

这两个区块被一个「父区块 parent」包含着。

这段代码有问题吗？　当然没问题啦，不信我们使用谷歌浏览器检查一下。

![](/image/back-2-font-xinfa-1/second-check-child.png)

子元素宽度和高度都是 OK 的。

我们再来看看它父元素高度和宽度。

![](/image/back-2-font-xinfa-1/parent-check.png)

我去！父元素的高度竟然是 **０**！！　我是不是安装了假的浏览器？

解铃还须系铃人，要想知道原因，我们需要去了解一下　DIV　标签和「浮动」的之间的关系。


## 这也是一个简单的浮动布局

还是一个很简单的布局，我们有三个DIV区块，分别为「first」、「second」、「third」它们父区块「parent」包含着

并且设置三个DIV区块为左浮动的。

代码如下：
{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>清除浮动演示</title>
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
    </head>
    <body>
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
    </body>
    </html>
    
{% endcodeblock %}

![效果图](/image/back-2-font-xinfa-1/div-float-1.png)


我们使用谷歌浏览器来检查一下，发现子元素的高度和宽度和设置的值一样，但是父元素高度还是为０。

![](/image/back-2-font-xinfa-1/origin-parent-height.png)


现在我们有一个需求：在保持浮动的情况下，让第三块区域在下一行显示，如何去做？

这就要使用到另一个招式 「清除浮动」

我们只要在第三块区块的「.third」中添加
{% codeblock lang:html %}
    clear: left;
{% endcodeblock %} 

即可。

来来来，代码敲一波：

{% codeblock lang:html %}
    <!DOCTYPE html>
        <html>
        <head>
            <title>清除浮动演示</title>
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
            </style>
        </head>
        <body>
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
        </body>
        </html>

{% endcodeblock %}

话不多说，让我们先看效果。

![添加 clear: left;的效果图](/image/back-2-font-xinfa-1/add-clear-left-1.png)

真的把第三个区块给压下来了！



还是按照上面的套路，我们来分析这段代码「 clear: left; 」：

它用到的关键字是「 clear 」，它的含义是清除。

而 「 left 」 代表是左边。

那么它合起来的意思是：**清除左边的浮动元素**。

说到更明白一点就是：**不让当前元素的左边有浮动元素。**

当前元素是第三个区块，它的左边有两个浮动元素 first 和second 。

那这段代码的意思就是：**不让第三个区块左边有浮动元素**，那浏览器这么处理呢？

浏览器会说：“老三啊，你的左边有两个浮动元素，但是你又不想左边有浮动元素，所以你就吃点亏，到 **下一行** 来吧！”

于是第三个区块就到了下一行啦。  

虽然有点复杂，但是按照上面思路进行一步步分解，是不是就变的很简单呢？

让我们再来拓展一下，clear 除了可以设置为 left,还可以设置为 「 right 」 和 「 both 」。

我相信不用我说你们也明白了吧！

right 就是不让当前元素 **右边** 存在浮动元素嘛。

那 both 就是不让当前元素的 **两边** 出现浮动元素喽。

说了这么多，我们好像还有一个问题没有解决，父元素的高度还是0啊！

这个有点尴尬！

## 父元素高度真的需要吗？

![](/image/back-2-font-xinfa-1/clear-float-parent.png)

那么，在解决之前，按照我们的套路，我们要问一个问题：我们可以不管父元素的高度吗？反正布局都设计出来了。

我的实习导师告诉我：“这是不行的。**浮动布局的占位空间往往是我们理想的父元素高度。**”

他说的太抽象，我们来说的直白一点：

「在布局中我们往往使用浮动布局来实现某一块区域的布局」。

「然后我们最好用一个DIV去包裹整个浮动布局，用于和其它的布局区分开」。

「最后我们要求这个父DIV和高度要和浮动布局的高度一样」。

对于上面的三点内容，前两点我们已经做到了。在上面的代码中，我们使用「parent」包裹着「first」、「second」、「third」，并且

而且它们都是浮动的。

想要解决第三个问题，就是使用我们上面讲的招式-**清除浮动**。

我们在父区块最后面增加一个空的DIV,将它设置它为「 clear:both 」。

为什么要这么使用呢，具体的原因我也讲不清楚，大概是这样的吧：

因为浮动元素会影响它的位置，我们必须确保它在父元素的最后一行，不受 **任何** 浮动元素干扰,我们必须清除浮动对它的干扰。

注意哦！最后一个元素不是浮动的哦。

来来来，代码走一波！

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>DIV和CSS的前世今生</title>
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
    </head>
    <body>
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
    </body>
    </html>

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

我们可不可以在某个地方统一定义，然后全局使用呢？就像Java一样，一次编译，到处运行。

哈哈哈，程序员都喜欢偷懒，我也不例外。回答是当然可以啦。

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

是不是很神奇呢？我们可以使用 CSS 自动的添加元素。并且设置其属性。

那么还有一个问题，如何让上面的这个代码被所有的父元素共享呢？

这个很简单啦，只要把需要样式的父元素的 class属性设置值为 「parent」，那么就可以直接使用啦。

来来来，让我们完整的敲一次代码！

{% codeblock lang:html %}
    <!DOCTYPE html>
    <html>
    <head>
        <title>DIV和CSS最佳实践</title>
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
    </head>
    <body>
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
    </body>
    </html>
    
{% endcodeblock %}

![使用最佳实践-伪类后的效果图](/image/back-2-font-xinfa-1/div-float-after.png)

## 你说的都是真的吗

从头到尾说了这么多，有的同学或许会问，你说的都是真的吗？，那些大型网站的页面都是按照你说的这么设计的的吗？

我读书少，你可别骗我。

不信？

我们去看看一些大型网站的页面的浮动布局的设计代码。

我们先看豆瓣的

![使用after](/image/back-2-font-xinfa-1/douban-1.png)

![使用after+浮动布局](/image/back-2-font-xinfa-1/douban-2.png)

我们再看看天猫的

![使用after](/image/back-2-font-xinfa-1/tianmao-1.png)
![使用after+浮动布局](/image/back-2-font-xinfa-1/tianmao-2.png)

哈哈，我可没有骗你们哦！大型网站都是这么用的，而且貌似高手都使用 「float:right;」呢！


最后，我们再来回顾一下，这篇文章从介绍　HTML的元素种类开始，到浮动布局，最后以清除浮动和最佳实践收尾。

可以让一个小白慢慢的窥视到大型网页设计的理念。

中间有一些招式可能说的太粗糙。不要捉急，我们先学心法。招式到后面慢慢再学习。

这篇文章到这里就结束了，我会在下一篇博文继续介绍心法 II

