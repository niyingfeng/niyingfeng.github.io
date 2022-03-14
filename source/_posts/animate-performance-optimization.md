---
title: 关于动画到硬件加速的那点事儿
date: 2015-09-30
tags: CSS,动画性能
---

目前我们关于实现动画的方式，无非就是类似 jQuery的动画， CSS 的 animation，transition，tranform 或者 requestAnimationFrame 以及触及GPU的硬件加速动画。 jQuery的动画不用说，就普通的 CSS 动画而言使用浏览器自身的渲染引擎来处理动画的渲染，而触发 CSS 硬件加速则是通过 GPU 来提升 CSS 动画的性能，将有关于动画的运算从 CPU 移植到 GPU 上，大大提高渲染动画的效率。GPU 在对于渲染图形方便的优势，所以能在渲染动画上更容易达到每秒60帧的顺滑效果，使得视觉上顺畅无比。 简单来讲，当一个页面初次加载完毕的时候，浏览器的渲染引擎便开始了它的工作：

*   浏览器解析HTML， 构建一棵 DOM 树，其每个节点对应页面的每个元素。
*   渲染引擎对 DOM树，以浏览器解析后的 CSS ，会对应构建出一棵 Render 树，其每个节点 RenderObject包含了所对应 DOM 的最终样式信息。
*   基于 Render 树，渲染引擎会将其中的某些特定节点同时创建新的 RenderLayer，从而还会生成一棵 RenderLayer 树。
*   当 Render 树和 RenderLayer 树构建完毕之后，便依据两者，遍历各个节点开始渲染绘制出页面。

![](http://blog.chinaunix.net/attachment/201509/30/26672038_14435988628aR6.png) 再简单介绍下 chrome 的 timeline 的几个名词：

*   Timer Fire： 定时器触发，很简单，比如 jquery 的动画均是通过 setTimeout 来处理生产动画效果（一般性的动画），所以每次展现完毕后都会触发下一个定时器事件 JS 的过程。
*   Recalculate Style：将 CSS 构建与 DOM 对应的的树，一般称之为 Render 树的过程。
*   Layout：依据 Render 树，以及 RenderLayer 树进行页面布局的过程。
*   Print：更具布局，绘制出页面的过程
*   Composite Layers：渲染展现到屏幕的过程

最古老的动画形式： HTML：

```
<div class="square"></div>
```

CSS：

```
.square{
        position:relative;
        top:50px;
        left: 50px;
        margin-bottom: 10px; 
        height:80px;
        width:80px;
        background-color:#999;
}
```

JS：

```
$('.jquery').click(function(){
        $('.square').animate({
                height: 300,
                width: 300,
                left:200,
                top:200,
                opacity: 5},
                3000, 'linear', function() {
        });
});
```

以上是一个很简单 jq 动画效果，3秒钟从原有图形样式转变到指定的图形样式，但是，浏览器所处理的却是一段 jq 分割的复杂的过程， ![](http://blog.chinaunix.net/attachment/201509/30/26672038_14435978423d0d.png) 每一次浏览器都要处理包括 Timer Fire，Recalculate Style，Layout，Paint，Composite Layers，一次又一次的不断重复又循环，内存占用率也是随之增大。 ![](http://blog.chinaunix.net/attachment/201509/30/26672038_1443597942uS7O.png) 如今的时代，css的动画帮我们解决了很多难题， 普通的2D CSS动画： CSS：

```
.css2d{
        width:300px;
        height:300px;
        left:200px;
        top:200px;
        transition: width 3s linear, height 3s linear,top 3s linear,left 3s linear;
        -webkit-transition: width 3s linear, height 3s linear,top 3s linear,left 3s linear,;
}
```

JS：

```
$('.css-2d').click(function(){
        $('.square').addClass('css2d');
});
```

直接上图，计算样式以及重新布局均有浏览器自身完成，并且少了定时器触发事件这一块，使得动画运行方式更为自然， ![](http://blog.chinaunix.net/attachment/201509/30/26672038_1443598040RPIT.png) 还有内存图： ![](http://blog.chinaunix.net/attachment/201509/30/26672038_1443598115lJHb.png) 或许感觉还不错了，但是由于涉及到了一些或导致重排的属性，使得动画的时候一直重排重绘，那么来看看硬件加速的样式动画吧， 吊炸天的硬件加速CSS动画： CSS：

```
.hardware{
        -webkit-animation: hardware 2s infinite linear;
        animation: hardware 2s infinite linear;
}

@-webkit-keyframes hardware {
        0% {
                transform: translate3d(0,0,0) scale3d(1,1,1);
                -webkit-transform: translate3d(0,0,0) scale3d(1,1,1);
        }


        50% {
                transform: translate3d(150px,150px,0) scale3d(3,3,1);
                -webkit-transform: translate3d(150px,150px,0) scale3d(3,3,1);
        }
}


@keyframes hardware {
        0% {
                transform: translate3d(0,0,0) scale3d(1,1,1);
                -webkit-transform: translate3d(0,0,0) scale3d(1,1,1);
        }


        50% {
                transform: translate3d(150px,150px,0) scale3d(3,3,1);
                -webkit-transform: translate3d(150px,150px,0) scale3d(3,3,1);
        }
}
```

JS：

```
$('.css-hardware').click(function(){
        $('.square').addClass('hardware');
});
```

对于硬件加速的动画，浏览器渲染的过程： ![](http://blog.chinaunix.net/attachment/201509/30/26672038_1443598224ob6h.png) 屌不屌其实都一样啦，只不过没有使用处理元素的一些，如height， width，left，top 等元素值的动画，而使用了一些3D CSS属性达到了我们所需的动画效果，但是浏览器处理动画却已经完全不一样。动画过程优化到了仅剩Recalculate Style，Composite Layers 两个过程。 ![](http://blog.chinaunix.net/attachment/201509/30/26672038_1443598249rf4X.png) 这边为什么会这么吊呢，这就全依仗与 对应所生成的第三种树 RenderLayer 树，该元素节点被渲染引擎根据 CSS 创建了对应的 RenderLayer 树节点，并将其绑定到 GPU，当该 RenderLayer 的 transform 等变化时候，跳过浏览器的 Layout，以及 Print，直接有 GPU 对其做变换。这个也是直接提升动画性能，内存突然降低（计算被移植到了GPU）的原因。 那么如何来处理 CSS，将我们所需要进行动画的元素构建时拥有新建的 RenderLayer 节点呢？ 目前查询到的资料来看，chrome 以下：

*   3D或透视变换（perspective transform）CSS属性
*   使用加速视频解码的\\<video>节点
*   拥有3D（WebGL）上下文或加速的2D上下文的\\<canvas>节点
*   混合插件（如Flash）
*   对自己的opacity做CSS动画或使用一个动画webkit变换的元素
*   拥有加速CSS过滤器的元素
*   元素有一个包含复合层的后代节点（一个元素拥有一个子元素，该子元素在自己的层里）
*   元素有一个z-index较低且包含一个复合层的兄弟元素（换句话说就是该元素在复合层上面渲染）

一般我们都会尝试对需要进行动画的元素添加一个 3D 的属性从而来触发其硬件加速的动画效果，现在又有一个专门用作触发生成新 RenderLayer 的属性值 will-change。 当然以上动画最优情况只正对于部分 GPU 加速处理的 CSS 属性，对于涉及到重排 relayout 和 重绘 repaint 的属性我们可能需要慎重考虑以及优化了。 `重排：`重排是页面结构调整，获取或者设置元素某些属性值时，触发了浏览器 Layout 过程重新定义的情况，比如调整元素的几何大小，位置，获取宽高等。重排必然导致重绘，浏览器渲染总会依照 Layout，Paint，Composite Layers 的流程来处理。下面是触发重排的一些主要情况：

*   脚本操作DOM，增加删除可视的DOM节点
*   操作 class 属性，或者设置 style 属性
*   计算获取当前 offsetTop/Left/Width/Height, scrollTop/Left/Width/Height, clientTop/Left/Width/Height, width, height 等属性值
*   内容变化， 窗口调整等

`触发重排的属性：` `盒子模型的相关属性：`width, height, padding, margin, display, border `定位特性的相关属性：`top, bottom, left, right, position, float, clear `内容的相关属性：`text-align, overflow, font-family, line-height,font-size `重绘：`重排必然导致重绘，并且对于仅调整各类属性色值等时，浏览器重新绘制输出到浏览器的情况。 触发重排的属性：color, border-style, visibility, background, outline, box-shadow 等 一些动画的重排重绘我们不可避免，我们要做的并不是禁止它，存在即合理，事实只有我们滥用。以下是一些简单的优化重排，重绘的方法：

*   将多次需要频繁改变的样式一次性完成，不要频繁的获取计算样式。
*   将大量的操作 DOM 操作放置到页面之外进行。
*   尽可能在最末端需要调整的 DOM 上改变 class，减小对其它 DOM 的影响。
*   动画尽量应用到 position 为 absolute 或者 fixed 的元素上，以便当前元素的动画造成大规模的重排。