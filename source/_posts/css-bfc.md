---
title: BFC 详解
date: 2016-06-30
tags: 
- BFC
- bfc
- css
---

### 你想要处理以下问题时

1.  两个块级元素上下元素 margin-bottom 与 margin-top 重叠。
2.  父级元素未将第一个子元素的margin-top包含在内部。
3.  图文混排中，文字排列会绕着浮动的图片进行排列，而不是左右分块。
4.  父级元素高度无法被浮动的子元素撑开。
5.  ...

然后我们就使用一系列"网上教程"进行解决~
### BFC是啥
BFC 意为 block formatting contexts，块级格式化上下文。 `w3c的定义如下：`

> 浮动元素，绝对定位元素，非块级盒模型的块级容器（比如 inline-block，table-cell以及table-caption的），以及 overflow 不为 visible 的块级盒模型 （除非该值已经被渲染到可视区域）均会为他们的内容创建一个新的块级格式化上下文。 在一个块级格式化上下文中，块级元素会从改块级元素的顶端从上往下一个一个依次布局。两个相邻的块级元素之前的垂直间距由他们的 margin 值决定。两个相邻块级元素的垂直距离会发生叠加。 在一个块级格式化上下文中，每一个元素框的左外边缘与容器包含块左边缘相接触（从右到左的模式就是右边缘接触）即使纯在浮动也是如此（虽然在存在浮动的情况下，元素的框高会塌陷）除非创建一个新的块级格式化上下文（在该情况下元素有可能由于浮动变窄）。

### 简单的说：

`FC：` 格式化上下文，页面中独立的一个渲染区域，以及自身的一套渲染规则，决定子元素的定位于相关作用。主要包括BFC和IFC `BFC：` 块级格式化上下文，独立的块级渲染区域，该区域拥有自身的一套渲染规则来约束块级盒子的布局，与区域外部无关。 `IFC：` 行内格式化上下文。

### 块级元素与BFC

是不是会感觉块级元素与BFC是不是有些类似，又有挺大差别。直接说的话，块级元素是一个实实在在的你所能看到的一个个块级的DOM节点，而BFC是较为虚拟的存在，是拥有对某一区域实现渲染的一套规则的虚拟概念。所以你才可以使用我们所说的BFC去解决块级元素或者行内元素的一些样式问题，这正是因为在你对渲染区域（或者说某一DOM节点）创建新的BFC后而改变了该渲染区域的某一些样式规则来完成你所期望的某种样式实现而已。 一般来说可以看作页面所有的元素处于一个BFC之中（除去内部生成的BFC），因为根节点便是一个BFC。
### BFC是如何创建的

*   根节点元素
*   浮动元素
*   position 为 absolute 或者 fixed 的元素
*   overlay 不为 visible 的元素
*   dispaly 为 inline-block table-cell 或者 table-caption 的元素

### BFC的渲染规则
将w3c的定义详细拆解，可以如下定义： - 内部元素会从区域顶部垂直一个接一个放置。 - 元素垂直方向上的间距由margin决定，属于同一BFC下的相邻元素，垂直的margin会发生重叠。 - 每个元素的左外边会与包含它的BFC的左内边接触，忽略是否有浮动的的兄弟节点。 - BFC区域不会与浮动元素发生重叠。 - BFC计算高度时，会计算其内部浮动元素。 - BFC为页面独立区域，内外元素不会相互影响。
### BFC使用实例
_文字环绕，重叠问题处理_

```
```
<style>
    body {
        width: 150px;
    }
    .float {
        width: 50px;
        height: 100px;
        float: left;
        background: #A3F173;
    }
    .main{
        height: 200px;
        background: #5C9E4C;
    }
</style>
<body>
    <div class="float"></div>
    <div class="main"></div>
</body>
```
```

![](http://freefe.cc/blog/wp-content/uploads/2016/06/20160630191811.png) class 为main 的div被浮动元素覆盖，如内部有文字时，将对其环绕展现，区域被浮动元素覆盖。当将其 BFC 化之后,就会展现BFC区域不会与浮动元素发生重叠的特性

```
```
.main{
    height: 200px;
    background: #5C9E4C;
    overlay： hidden;
}
```
```

![](http://freefe.cc/blog/wp-content/uploads/2016/06/20160630192105.png) _清除浮动，父级元素高度计算内部浮动元素_

```
```
<style>
    .main{
        padding: 5px;
        width: 200px;
        background: #5C9E4C;
    }
    .float {
        width: 50px;
        height: 100px;
        float: left;
        background: #A3F173;
    }
</style>
<body>
    <div class="main">
        <div class="float"></div>
    </div>
</body>
```
```

![](http://freefe.cc/blog/wp-content/uploads/2016/06/20160630193213.png) 父级元素内部元素由于其浮动属性，所以父级元素的高度并未计算浮动元素的高度，当将其 BFC化之后，则展现BFC计算高度时，会计算其内部浮动元素

```
```
.main{
    overflow：hidden;
    padding: 5px;
    width: 200px;
    background: #5C9E4C;
}
```
```

![](http://freefe.cc/blog/wp-content/uploads/2016/06/20160630193540.png) 不过上面形式带来的问题是，我们需要将父级元素设置某一属性，使其被BFC化，就很可能会有样式上的冲突问题，所以更好的解决方案为：

```
```
.main:after {
    content: " ";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
}
```
```

_margin 重叠问题_

```
```
<style>
    .top{
        margin-bottom: 20px;
        height: 100px;
        width: 200px;
        background: #5C9E4C;
    }
    .bottom {
        margin-top: 30px;
        height: 100px;
        width: 200px;
        background: #A3F173;
    }
    .center{
        margin-top: 40px;
        height: 50px;
        background: #5C9E4C;
    }
</style>
<body>
    <div class="top"></div>
    <div class="bottom">
        <div class="center"></div>
    </div>
</body>
```
```

![](http://freefe.cc/blog/wp-content/uploads/2016/06/20160630195342.png) 上面div的margin-bottom为20px,下面div的margin-top为30px，其内部的div的margin-top为40px，最后的最后，两个div之前的间距为 - 40px！这便是块级元素margin叠加与塌陷的问题，可以理解为改三个div均属于同一个BFC下的元素，所以垂直的margin会发生重叠和塌陷。当将下面的div进行BFC话之后你会看到： ![](http://freefe.cc/blog/wp-content/uploads/2016/06/20160630195733.png) 所以BFC为页面独立区域，内外元素不会相互影响。
### 总结
个人认为，了解BFC的渲染特性，有助于在我们平时开发时遇到的一些样式问题的解决，布局。将其作为一个知识点进行了解对我们CSS的开发具有比较大的帮助。包括对于清楚浮动，边距布局等控制与了解。