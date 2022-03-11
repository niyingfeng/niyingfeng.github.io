---
title: 居中等高方案集锦
date: 2017-02-07
tags: CSS,垂直居中,水平居中,等高对齐
---
## 水平居中

- 元素使用固定宽度，margin左右值设为auto。（元素须为确定宽度）
- 父元素设置text-align:center，子元素设置display:inline-block。（需要处理inline-block带来的兼容性问题）
- 父元素偏移外层区域的50%宽度，子元素偏移父元素的50%宽度，达到居中效果。（实现较为复杂，需要固定父级宽度）
- 元素偏移外层区域的50%，负margin来偏移元素的一半宽度。（实现较为复杂，需要固定宽度）
- CSS3的fit-content方法：width: fit-content;（兼容性问题）
- Flex实现：justify-content: center;（兼容性问题）



## 垂直居中

- 将line-height设置成和height值一样。（仅限于单行文字与图片居中）
- 绝对定位，并且定位高度top:50%，负margin偏移高度的一半。（需要固定高度）
- 模拟table样式，父级元素设置display:table，子元素设置diapaly:table-cell，vertical-align:middle即可。（增加结构的复杂度，IE6,7下不支持）
- 添加一个空标签，高度为父元素的50%，负margin为元素高度的一半。（需要固定高度，并且添加无意义标签）
- 添加一个空标签，将其与目标元素，display:inline-block，vertical-align:middle，然后借助该空元素的高度设置为父级的100%，使得目标元素实现垂直居中。（多添加空标签）
- Flex实现：align-items: center;（兼容性问题）


## 等高对齐

- 背景图片的假等高展现，使用多色背景图填充容器背景来伪装成等高。（并非真正的等高，背景伪装）
- 多层元素嵌套并且偏移，实现背景色上的等高，内部嵌入相关元素，随一高度变化，外层背景元素均会被撑开到最大元素等高。（实现结构过于复杂）
- 使用边框来伪装另一列的背景色。（多列会有问题）
- 所有列中使用正的上、下padding和负的上、下margin，并在所有列外面加上一个容器，并设置overflow:hiden把溢出背景切掉。（底部边框会有问题，使用添加一个元素来模拟边框效果）
- 模拟表格布局来实现等高。（IE低版本不兼容）
- Flex实现：align-items: stretch;（兼容性问题）



来源: [http://www.w3cplus.com/](http://www.w3cplus.com/)