
---
title: 后端工程师入门前端页面重构（二）：心法 I
---

上一篇博客我们介绍了布局的口诀，这篇博客我们介绍布局的心法－**清除浮动**。

「清除浮动」这个四个字现在看来有点难懂，我们先不管它。

我们先来写一个简单的浮动布局。

## 一个简单的浮动布局
<code>

    <!DOCTYPE html>
    <html>
    <head>
    	<title>简单的浮动布局</title>
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
    
    <div class="block-1">
    	这是第一个区块
    </div>
    
    <div class="block-2">
    	这是第二个区块
    </div>
    </body>
    </html>
       
<code/>

![效果图](/image/back-2-font-xinfa-1/float-layout-show-1.png)

上面的这段代码很简单，我们使用　DIV　标签画了两个　200 * 200　的区块，它们分别是「block-1」、「block-2」，并且设置它们为「左浮动」，然后发现它们的彼此紧挨着。

这段代码没问题，我们可以使用谷歌浏览器来检查一下它们的高度和宽度，和我们设置的完全一样。

![](/image/back-2-font-xinfa-1/div-show-height.png)

如果你想看看它们浮动前样子，可以把两个 DIV 的　<code>float: left;</code> 注释掉，我保证你会被它们的样子吓到。

## 还是一个简单的浮动布局
<code>

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
    
</code>

![效果图](/image/back-2-font-xinfa-1/float-layout-show-2.png)

这段代码还是很简单，我们还是使用　DIV　标签画了两个　200 * 200　区块，和之前代码的唯一区别在于

这两个区块被一个「父区块 parent」包含着。

这段代码有问题吗？　当然没问题啦，不信我们使用谷歌浏览器检查一下。

![](/image/back-2-font-xinfa-1/second-check-child.png)

子元素宽度和高度都是 OK 的。

我们再来看看它父元素高度和宽度。

![](/image/back-2-font-xinfa-1/parent-check.png)

我去！父元素的高度竟然是 **０**！！　我是不是安装了假的浏览器！

解铃还须系铃人，要想知道原因，我们需要去了解一下　DIV　标签和「浮动」的之间的关系。


## 这也是一个简单的浮动布局

还是一个很简单的布局，我们有三个DIV区块，分别为「first」、「second」、「third」它们父区块「parent」包含着

并且设置三个DIV区块为浮动的。

代码如下：
<code>
    
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
    
</code>

![效果图](/image/back-2-font-xinfa-1/div-float-1.png)


我们使用谷歌浏览器来检查一下，发现子元素的高度和宽度和设置的值一样，但是父元素高度还是为０。

![](/image/back-2-font-xinfa-1/origin-parent-height.png)


现在我们有一个需求：在保持浮动的情况下，让第三块区域在下一行显示，如何去做。

这就要用到我们这篇博文的主题：「清除浮动」。

我们只要在第三块区块的「.third」中添加<code>clear: left;</code> 即可。

话不多说，让我们先看效果。

![添加 clear: left;的效果图](/image/back-2-font-xinfa-1/add-clear-left-1.png)

真的把第三个区块给压下来了！

让我们再次来分析这段代码<code>clear: left;</code>：

它用到的关键字是 **clear**，它的含义是**不让当前元素的的左边或者右边存在浮动元素**。

当前元素是第三个区块，它的左边有两个浮动元素 first和second。

那这段代码的意思就是：**不让第三个区块左边有浮动元素**，那浏览器这么处理呢？

浏览器会说：“老三啊，你的左边有两个浮动元素，但是你又不想左边有浮动元素，所以你就吃点亏，到下一行来吧！”

于是第三个区块就到了下一行啦。  

是不是很简单呢！

其实clear还可以有right和both,用法和left是一样的。只不过both是左右两边都不允许有浮动元素。

说了这么多，我们好像还有一个问题没有解决，父元素的高度还是0啊！

## 父元素高度真的需要吗？

![](/image/back-2-font-xinfa-1/clear-float-parent.png)

那么，在解决之前，我们有一个问题：我们可以不管父元素的高度吗？反正布局都设计出来了。

我的实习导师告诉我：“这是不行的。**浮动布局的占位空间往往是我们理想的父元素高度。**”

他说的太抽象，我们来说的直白一点：

「在布局中我们往往使用浮动布局来实现某一块区域的布局」。

「然后我们最好用一个DIV去包裹整个浮动布局，用于和其它的布局区分开」。

「最后我们要求这个父DIV和高度要和浮动布局的高度一样」。

对于上面的三点内容，前两点我们已经做到了。在上面的代码中，我们使用「parent」包裹着「first」、「second」、「third」，并且

而且它们都是浮动的。

其实第三个我们可以这么解决：我们在父区块最后面增加一个空的DIV,让设置它为clear:both。

为什么要加上<code>clear:both</code>呢？

**因为浮动元素会影响它的位置，我们必须确保它在父元素的最后一行，不受其它浮动元素干扰,我们必须清除浮动对它的干扰。**

注意它不是浮动元素哦。

<code>

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

</code>

然后我们会发现，父元素竟然有高度了，而且和浮动元素的高度是一样的！
![父元素有高度了](/image/back-2-font-xinfa-1/parrent-height.png)


## 清除浮动－最佳实践

上面解决问题的方法是我们在每个父区块最后添加一个空的 DIV，这样的做法是**低效的**！

因为一个页面有成百上千个布局区块，如果每一个我们都手动写一个空的　DIV，增加工作量不说，日后维护起来，也很麻烦。

我们可不可以在某个地方统一定义，然后全局使用呢？

回答是肯定。

这就要使用到 CSS 的「伪类」,伪类的具体用法可以参考[W3C的CSS伪类教程。](http://www.w3school.com.cn/css/css_pseudo_elements.asp)

我们这里就给出如何使用伪类来清除浮动的最佳实践的代码。

这里使用的是「after」,它可以在被指定的元素最后添加元素，并且设置被添加元素「内容为空」，「清除浮动」和「块级显示」这三个属性。

<code>
    
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
    
            /*
              after 伪类
             */
            .parent:after{
                content: "";
                clear: both;
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
    
</code>

![使用伪类后的效果图](/image/back-2-font-xinfa-1/div-float-after.png)

这篇文章我们主要介绍了清除浮动和它的最佳实践，你们学会了吗？是不是感觉很简单呢？

我们会在下一篇博文继续介绍心法