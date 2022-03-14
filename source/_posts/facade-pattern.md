---
title: JS设计模式五：门面模式
date: 2014-04-09
tags: JS设计模式,设计模式,门面模式
---

拖拖拖拖啦啦啦啦啦~ n久未更新更新JS设计模式了，不能回家墓祭祖先，那就在此墓祭墓祭JS吧，T T（本文实例主要来自 JavaScript设计模式）。

之前在桥接模式中简单的说过（额，桥接模式有点混，下次再写一写），门面模式本质是实现一个简单的统一接口来处理对各个子系统接口的处理和调用。其和桥接模式的不同之处便在于桥接模式中的各个类是完全独立的，桥接模式只在必要的时候将这些类关联起来。

而门面模式则有点不同。门面模式其实可以很形象的比作为一家咖啡店的店面窗口，客户只需要说明自己是需要摩卡还是白咖啡，也就是说咖啡店提供给客户的只是各类咖啡的选择接口，而将内部的各个子类行为封装起来，比如磨咖啡豆，冲泡，加奶等过程并不展现给客户，因为每一种咖啡有其不同的生产形式，这样也使得客户更方便的选择所喜欢的咖啡。

门面模式的优点就在于我们将客户与较为复杂的子系统方法和接口分离开来，降低用户与各个子系统间耦合度来提高代码质量。

![](http://image.freefe.cc/20170306195916.png)

下面是一个纯粹形式化的例子：

```
function a(x){
    // do something
}
function b(y){
    // do something
}
function ab( x, y ){
    a(x);
    b(y);
}
```

当然形式上与桥接模式有很大程度上的类似，下面的小例子可以感受下其和桥接模式的不同

```
var N = window.N || {};
N.tools = {
    stopPropagation : function( e ){
        if( e.stopPropagation ){
            e.stopPropagation();
        }else{
            e.cancelBubble = true;
        }
    },

    preventDefault : function( e ){
        if( e.preventDefault ){
            e.preventDefault();
        }else{
            e.returnValue = false;
        }
    },
    
    stopEvent : function( e ){
        N.tools.stopPropagation( e );
        N.tools.preventDefault( e );
    }
}
```

一个小的阻止事件冒泡以及阻止默认事件工具方法，从其代表性的 stopEvent 不难看出其实质与桥接模式的区别。其上面两个方法 stopPropagation 和 preventDefault 很会令人觉得类似于适配器模式，的确是很类似与适配器模式，不过适配器模式的主要针对于将接口进行适配包装，使其适用于各种不同兼容性的环境。按本人的理解则是，如果提供兼容性的信息使其方法在不同环境下生成不同的方法，比如一些匿名自调用函数根据判断返回不同函数的类似形式，而不是说每次运行再在函数内部进行判断运行，则称其为适配器模式可能为更恰当一些~

对于门面模式的一大好处就是对函数的组合上，犹如上面纯粹模式的例子，门面模式形成的组合函数又称为便利函数（convenience function），便利，便利，便利，好吧，可能对于门面模式的概念又要模糊了，来一例:

我们需要将id为content的div元素设置文本颜色 red，那么简单的代码,

```
var element = document.getElementById('content');
content.style.color = 'red';
//如还想设置字体大小为20px
content.style.fontSize = '20px';

那么当有一个元素需要设置多种属性时，

function setStyles( id, styles ){
    var element = document.getElementById( id );
    for( var key in styles ){
        if( styles.hasOwnProperty( key ) ){
            element.style[ key ] = styles[ key ];
        }
    }
}

setStyles( 'content', {
    color : 'red'，
    fontSize : '20px'
} );
```

setStyles就相当于一个便利函数，也可以视作为门面元素，不过是相对于最简单一类。

如果说是具有好几个元素，均需要设置相同的一批属性的话，那么将 setStyles 包装一下，将其嵌入在另一个门面元素之中，组合成为一个结构相对复杂的门面模式实例：

```
function setCSS( ids, styles ){
    for( var i = 0,len = ids.length; i<len; i++ ){
         setStyles( ids[i], styles );
    }
}
```

从setCSS中可以看出，对于使用setCSS的用户来说，根本不知道其内部的 setStyles 代码形式。可想而知，当一块代码逻辑较为复杂，调用许多各个接口等的时候，我们使用门面模式将其封装，可以带来很大的便利性。

## 总结

门面模式大致上可以分为两个小类， 某块代码反复出现，比如函数a的调用基本都出现在函数b的调用之前（虽然基本上没有那么简单的形式），那么你可以考虑考虑将这块代码使用门面实例包装包装来优化结构。还有一种即是对于一些浏览器不兼容的API，放置在门面模式内部进行判断，处理这些问题最好的方式便是将跨浏览器差异全部集中放置到一个门面模式实例中来提供一个对外接口。

当然也需要很注意的是，对于门面模式的滥用其产生的结果是超级严重的，不但是代码整体结构感觉较为散架，还是代码可读性严重降低，不寻找一处BUG可能需要从一个门面实例中找到另一个，再联系到第三第四个，会使代码维护的程序崽子非常反感的~