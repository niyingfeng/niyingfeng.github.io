---
title: 浅谈 CSS hack
date: 2012-03-03
tags: CSS,css hack
---

CSS hack是基于浏览器的不同，如IE，FF等，各个浏览器对于CSS的解析不同，就会导致页面产生一些差异，是我们的设计在不同浏览器上得到不同的效果。 这个时候我们就需要针对不同的浏览器去写不同的CSS，让它能够同时兼容不同的浏览器，能在不同的浏览器中也能得到我们想要的页面效果。 CSS hack大致有3种表现形式。CSS内部样式hack，选择器hack以及HTML头部引用（if IE）hack。 先来看看简单的CSS内部样式hack使用表：（括号中的 S 代表标准模式 Q 代表混杂模式） ![](http://freefe.cc/blog/wp-content/uploads/2016/06/csshack.png) 上面列表中很清楚的标出了各个hack对于版本的适用性，hack通常的用法就是以 适用性少的来覆盖适用性多的。（IE6 对于 !important 可以查看一些资料，并不是所说的那么简单） 如：（均以标准模式进行说明）

```
#test 
{ 
    width:300px; 
    height:300px; 
    background-color:blue; /*firefox*/
    background-color:red\9; /*all ie*/
    background-color:yellow\0; /*ie8*/
    +background-color:pink; /*ie7*/
    _background-color:orange; /*ie6*/
}
```

在该css中 ：

*   background-color:blue; 为标准的语句，是适用与所有的浏览器。
*   background-color:red\\9; 为适用与所有的IE浏览器。故而在所有的IE浏览器中会覆盖background-color:blue;的效果。
*   background-color:yellow\\0; 为适用与IE8的浏览器。
*   +background-color:pink; 为适用与IE6和IE7的浏览器（与 * 一样）。
*   _background-color:orange; 为适用与IE6的浏览器。

所以 一层层的适用，一层层的覆盖。从而达到了我们想要的，在不同浏览器下实现不同效果的目的。当然这不是真正的目的，CSS hack最多的还是用于在个别浏览器与其他浏览器有一定偏差时来进行CSS样式的改善，完善网站的美观性。以及代码的兼容性问题。 对于CSS选择器hack则较为简单: `1. IE6以及IE6以下版本浏览器` * html .demo {color: green;} `2. 仅仅IE7浏览器` *:first-child+html .demo {color: green;} `3. 除IE6之外的所有浏览器（IE7-9, Firefox,Safari,Opera）` html>body .demo {color: green;} `4. IE8-9,Firefox,Safari,Opear` html>/**/body .demo {color: green;} `5. IE9+` :root .demo {color: red;} `6. Firefox浏览器` @-moz-document url-prefix() { .demo { color: red; }} `7. Webkit内核浏览器（Safari和Google Chrome）` @media screen and (-webkit-min-device-pixel-ratio:0) {.demo { color: red; }} `8. Opera浏览器` @media all and (-webkit-min-device-pixel-ratio:10000), not all and (-webkit-min-device- pixel-ratio:0) {head~body .demo { color: red; }} `9. iPhone / mobile webkit` @media screen and (max-device-width: 480px) { .demo { color: red } } 最后一种就是较为官方的 IF IE的头部判断的hack方法。

1.  条件注释的基本结构和HTML的注释()是一样的。因此IE以外的浏览器将会把它们看作是普通的注释而完全忽略它们。
2.  IE将会根据if条件来判断是否如解析普通的页面内容一样解析条件注释里的内容。
3.  条件注释使用的是HTML的注释结构，因此他们只能使用在HTML文件里，而不能在CSS文件中使用。

对于判别符号

*   lte：就是Less than or equal to的简写，也就是小于或等于的意思。
*   lt ：就是Less than的简写，也就是小于的意思。
*   gte：就是Greater than or equal to的简写，也就是大于或等于的意思。
*   gt ：就是Greater than的简写，也就是大于的意思。
*   ! ：就是不等于的意思，跟java script 里的不等于判断符相同