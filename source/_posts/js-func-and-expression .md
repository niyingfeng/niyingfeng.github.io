---
title: javascript的函数声明与函数表达式
date: 2013-05-06
tags: JS函数声明,JS函数表达式
---

### 函数定义方式
在javascript中，定义一个函数大体上有3种方式：

*   函数申明
*   函数表达式
*   通过 Function 函数构造函数定义。

一般来说 方法3 不太常出现，仅仅只会在一些JS类库中实现比如解析json，解析函数的字符串格式等作用。今天主要来讲解下我所理解的前2种方式 `函数声明` 和 `函数表达式`。
### 函数声明
ECMA对于函数声明的定义为：

```
function Identifier ( FormalParameterListopt ) { FunctionBody }
```

使用函数申明的函数其名称会被提升，严格意义上讲就是在进入上下文阶段便已经处在的该上下文中的变量对象中（VO），位于代码执行之前。

```
alert(func);
function func(){
    return "a function";
}
```

很显然，alert 为 func函数。 在line-1中便可以访问func，就是所谓的函数使用函数声明产生的函数提升。函数作为ECMAScript三种代码类型中的一种（还有global和eval），代码的执行都会依赖自身的一个上下文，执行上下文分为2个阶段 进入执行上下文 和 代码执行。 该处的函数声明名称被放入变量对象中，是在进入执行上下文阶段，是在代码执行阶段之前，所以可以在代码执行阶段被访问到，而无关于在代码中声明位子进行的函数声明。关于执行上下文可以参考TOM大叔的 javascript核心 和 执行上下文 ，讲的很有深度的。 对于函数声明也是有规范的：要么处于程序级别，要么处于其他函数中。除了这2处，其他位置都是违反规范的，也会导致一些浏览器的解析不正确。 比如

```
function foo(){
    if(window===parent){
        function bar(){alert(1);}
    }
    else{
        function bar(){alert(2);}
    }
    bar();
}
foo();
```

显然讲函数声明置于if的块中是不符合ECMA规范的，一般浏览器会将 ![](http://image.freefe.cc/26672038_1367503582f6uL.png) ECMA中作用域只有函数级的作用域，而没有块级作用域，所以if的块级作用域是无效的。而在与火狐下 ![](http://image.freefe.cc/26672038_1367504437gxGP.png) 该问题就是由于火狐浏览器下对块内函数声明与一般的浏览器有差异导致的，所以需要注意对于一般函数声明需要有规范。
### 函数表达式
函数表达式的声明为

```
function Identifieropt ( FormalParameterListopt ) { FunctionBody }
```

与函数声明的区别在于，标示符名称可以省略，并且处于表达式中，为表达式的一部分。

```
var func1 = function f(){};
var func2 = function(){};
(function func3(){});
~function func4(){};
!function func5(){};
true,function func6(){};
...
```

以上等等，函数部分均为自身表达式的一部分，所以都为函数表达式。 函数表达式与函数声明的一个重要的区别在于，对于 var func1 = function f(){}; 来说，规范定义其中的 f 标示符不会暴露出来，仅仅可以供 f 函数内部进行访问。 当然在此对于IE8-版本的浏览器来说又有一个重大的BUG。 对于表达式 var func1 = function f(){}; 来说，其解析就会出现较为严重的错误，它会现将表达式中的函数部分单独提取出来先进行函数声明，在进行函数表达式解析。更为严重的是，其中的 func1 ！= f，相当于独自创建了 函数f ，再对表达式中的函数在创建一份，并且将地址赋予 func1。解决的办法就是尽量避免IE的这些特性保证兼容所有浏览器。
### 实例

#### e.g.1

```
alert(a);
var a = 2;
function a(){}
alert(a)
```

结果为 function a(){} 和 2。 首先在进入上下文阶段时 函数声明将变量声明覆盖掉（变量的赋值是处于代码执行阶段），所以第一个 alert 弹出的为函数，当代码执行过程中，a又被重新赋值为2，故第二个 alert 显示的为2。整个流程可以与 e.g.2 进行比较就不难理解了。

#### e.g.2

```
alert(a);
var a;
function a(){}
alert(a)
```

其结果均为 function a(){}。 就可以看出，将a赋值为2，是在代码执行阶段进行的。那么对于

#### e.g.3

```
alert(a);
var a = 2;
alert(a);
```

可以很清楚的知道 答案为 undefined 与 2 但是对于

#### e.g.4

```
alert(a);
function a(){}
var a = 2;
alert(a)
```

为什么又是 function a(){} 和 2 为什么之后的变量声明中 a为undefined没有覆盖函数声明的a呢？ 一般的看法为权重说法，就是函数声明一个标示符之后，如果再对该标示符进行变量声明，那么该变量声明将视为无效。而另一种看法是对于函数声明其实其流程与变量声明是一直的，在进入上下文阶段 均赋值为undefined，但是函数声明将函数引用赋给标示符这一过程，是在代码执行之前触发那一刻。 就好第一个 alert 之前那一刻，a从undefined变为了function。个人偏向于后一种看法。当然如果要知道真正的流程那么还是需要去看 ECMA3或者ECMA5详细规范吧（http://ecmascript.cn/ ）。 当然说到了函数，那么势必需要知道一下关于 this 的一些资料，可以参考下上篇文章 JS的this简单理解~ 文章下面有好几篇经典的 this 介绍。

#### e.g.5

```
var f = function(){ return true; };
var g = function(){ return false; };
(function(){
    alert(f());
    if(g() && [] !== []){
        f = function f(){ return false; }
        function g(){ return true; }
    }
})();
alert(f());
```

这是一个比较绕的题目，考察的就是上面说的2点。 先来看下答案 chrome opera IE9+等普通浏览器 会弹出 true false ，火狐这个文艺浏览器 会弹出 true true 而IE8-这2B浏览器就会弹出false true。 呵呵开玩笑。 首先说下普通浏览器 true false 吧，JS中只有函数作用域，而没有块级作用域，所以匿名自执行函数中判断的中的 g() 就为块中定义的函数g（所谓的函数提升），然后 f 被重新指向返回 false 的函数了。 所以结果为 true false。 那么第二种 true true的话，由于规范中的函数声明只可以在 程序级 或者 其它函数中，位于 if 块语句中显然会有悖于这个规范，而火狐是无法忍受该错误的，会将块中的函数声明作为 函数表达式的形式来处理。那么在 if 判断的时候就直接米有通过 那么就为 2个true了。

```
var g = function(){ return false; };
(function(){
    alert(g());
    {
        function g(){ return true; }
    }
    alert(g());
})();
```

从这段代码在火狐下执行可以看出来， false 和 true 相当于 火狐下，块中的函数声明被当做了函数表达式来处理（效果上可以这么说）。 最后对于IE8-下的浏览器来说对于有名称的函数表达式，该标示符会被暴露出来。确切的来讲 IE8-下 会将 f = function f(){} 先进行函数声明一次，然后再进行一次函数声明，也导致了该处的 f 并不是会到全局变量下的 f ，而是因为已经在该函数作用域下有了f的函数声明，而覆盖该函数作用域下的 f。则有了 true false 的结果。 呵呵 能力有限，菜鸟观点，还有很多地方没有弄清楚，望大家多多指教。