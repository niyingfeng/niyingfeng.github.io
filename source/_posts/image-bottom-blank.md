---
title: image标签底部留白问题原理以及解决方案
date: 2016-07-12
tags: image底部留白
---

### 那一缕白是什么情况
有时候会经常遇到这种情况，某一个元素内部放置一张图片，可是偏偏就是它，图片底部怎么得也不和外层元素的底部重合，留出一缕散发出蛋蛋的忧伤的那么些像素！ ![](http://freefe.cc/blog/wp-content/uploads/2016/07/20160707192440.png) 有经验的刷刷刷添加几个样式就规避掉了，新手就干瞪眼着瞎尝试。对于其本质原因也都忽视了。
### 细聊一下vertical-align
对于vertical-align相对于text-align来说接触会少很多，很大一个原因是我们对他不甚了解，导致很所时候需要该样式来处理的一些样式，我们宁可绕过去使用其他方式进行解决。 vertical-align 为定义行内元素的基线相对于元素所在行的基线的垂直对齐方式。 先简单来看下vertical-align的值表

| 值 | 描述 |
| --- | --- |
| baseline | 默认。元素放置在父元素的基线上。 |
| sub | 垂直对齐文本的下标。 |
| super | 垂直对齐文本的上标 |
| top | 把元素的顶端与行中最高元素的顶端对齐 |
| text-top | 把元素的顶端与父元素字体的顶端对齐 |
| middle | 把此元素放置在父元素的中部。 |
| bottom | 把元素的顶端与行中最低的元素的顶端对齐。 |
| text-bottom | 把元素的底端与父元素字体的底端对齐。 |
| length |  |
| % | 使用 "line-height" 属性的百分比值来排列此元素。允许使用负值。 |
| inherit | 规定应该从父元素继承 vertical-align 属性的值。 |

对应类似刚刚学习英文时候的英文练习本： ![](http://freefe.cc/blog/wp-content/uploads/2016/07/20160712193851.png) 看下图，只要这边为设置 img vertical-align 小实例（fg只是为了观察字母上下边界）： ![](http://freefe.cc/blog/wp-content/uploads/2016/07/20160712193227.png) 需要注意，这边我们设置的为 image的 vertical-align 的值。
### 原来如此
然后应该比较容易的理解，最上图出现的那一缕不和谐：`image标签为行内元素，直接导致其默认的垂直对齐方式为-baseline，故而图片的下底边对应了外层div的基线baseline，而baseline并不是外层元素的正真的底部（在未设置font-size为0时），而是高于底部的一条基线，所以，那一块空白区域就由此而生。` 那么解决方案也就明白了：

1.  设置image 的垂直对齐方式为： top, bottom, middle 等
2.  设置image display:block; 消除vertical-align 影响
3.  设置父级元素，font-size:0; 或者 line-height:0;(不推荐)

### 行内元素之间的空白
顺便也提起一下关于多个行内元素之前的空白

```
<div class="container">
    <span>1234</span>
    <span>1234</span>
    <span>1234</span>
    <span>1234</span>
</div>
```

![](http://freefe.cc/blog/wp-content/uploads/2016/07/20160712200206.png) 其实是由于空白元素造成的，起最好的解决方案为

```
.container span:after{
    content: ' ';
    font-size:0;
}
```