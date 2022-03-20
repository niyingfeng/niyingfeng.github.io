---
title: 关于0.5px的那点事儿
date: 2016-09-22
tags: 
- 0.5px
- 0.5px实现
- CSS
- 移动端0.5px
---

在retina显示屏下网页会由1px会被渲染为2px，那么视觉稿中1px的线条还原成网页需要css定义为.5px。大部分移动机型的css渲染不支持.5px，所以用了1px来替代，最终渲染为2px的粗线条，这个问题往往会被忽视，从而导致移动网页中普遍存在2px的粗线条。

### 原始方案

```
border: .5px solid #000;
```

### 方案一

通过`transform:scale(x,y)`来缩放线条为原来的一半，可显示0.5px的线条。

```
.item:after {
    position: absolute;
    content: '';
    width: 100%;
    left: 0;
    bottom: 0;
    height: 1px;
    background-color: red;
    -webkit-transform: scale(1,.5);
    transform: scale(1,.5);
}
```

### 方案二

先定义1px的边框，拉伸内容的宽度和高度为父级的2倍，使用`transform:scale(0.5)`缩放为原来的0.5倍

```
-webkit-transform: scale(5);
transform: scale(5);
```

### 方案三

以下形式的图片实现，可以用 gif，png，或 base64 图片 ![image](http://image.freefe.cc/20160922204325.png)

```
.border{
    border-width: 1px;
    border-image: url(border.gif) 2 repeat;
}
```

### 方案四

原理：利用 box-shadow 向内收缩后的残余阴影

```
.item {
    /* 模拟此效果：
    border: 1px solid rgba(0, 0, 0, .15);
    */
    box-shadow: 0 -1px 1px -1px rgba(0, 0, 0, .5),
                -1px 0 1px -1px rgba(0, 0, 0, .5),
                1px 0 1px -1px rgba(0, 0, 0, .5),
                0 1px 1px -1px rgba(0, 0, 0, .5);
}
```

### 方案五

原理：利用背景色在1px中渐变的效果实现渐变模拟

```
.item:after {
    content: "";
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 1px;
    background-image: -webkit-linear-gradient(0deg,#e0e0e0 50%,transparent 50%);
    background-image: linear-gradient(0deg,#e0e0e0 50%,transparent 50%);
}
```

### 方案六

通过 viewport + rem 实现的，在devicePixelRatio不同情况下输出不同 meta 的 viewpoint

```
<meta name="viewport" content="initial-scale=5, maximum-scale=5, minimum-scale=5, user-scalable=no">
```

### 推荐：

使用伪类+tranform的形式实现 0.5px！