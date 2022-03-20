---
title: 详谈JS闭包~
date: 2013-03-10
tags: 
- closure
- JS闭包
---

### 闭包的概念
首先也来简单的说说，JS的闭包到底是什么？较为官方的解释为：

> Closure : A "closure" is an expression (typically a function) that can have free variables together with an environment that binds those variables (that "closes" the expression). 所谓“闭包”，指的是一个拥有多个自由变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。

还有一个对于闭包的描述我认为也挺不错的：

> A closure is a combination of a code block (in ECMAScript this is a function) and statically/lexically saved all parent scopes.Thus, via these saved scopes a function may easily refer free variables. 闭包是一个组合的代码块（在ECMAScript中是函数），并且静态保存所有父级的作用域。通过这些保存的作用域来搜寻到函数中的自由变量。

按照以上两条描述其实可以看出，闭包不是一个名词，而更偏向于一个形容词来形容一种结构，`闭包不仅仅是简单地是在外部函数返回一个内部函数那么简单，事实上从广义的理论上来讲ECMAScript中所有的函数均可以理解为闭包。`说了那么多模糊的，那么来具体理解下，到底，什么才是闭包。
### 讲在闭包前的知识
首先要了解一些基本的与闭包有关的知识。(以下并不完全正确~) `执行上下文(execution context)：`执行上下文是ECMAScript中用来描述JS代码执行的一个抽象概念。所有的JS代码都是在某个执行上下文的环境中运行的。在当前执行上下文中调用另一个function的时候就会进入一个新的执行上下文环境，直到当前function执行完毕的时候就会离开此新的执行上下文环境，重新返回之前的执行上下文环境。来回的切换上下文执行环境也就构成了一个执行上下文堆栈。 `作用域链(scope chain)：`每一个执行上下文环境都会和一个作用域链相互关联。作用域链可在进入某一个执行上下文环境的时候来进行对标识符(identifier)求值。很多时候作用域链中不仅仅只有一个对象，可以想象作用域链也为一个堆栈，查找标识符会从链首开始，然后依次往后从对象中查找标识符，在对每个对象进行查找时也对其原型链进行查找。 当切换一个执行上下文环境的时候会进行以下的操作：

1.  当函数创建时(注意不是调用时)的时候已经静态的确定了[[scope]]属性(稍后会详细讲一下)。
2.  该处才是切换的时候进行的第一步操作，创建活动对象(activation object)，活动对象是在进入函数上下文时刻被创建的，它通过函数的arguments属性初始化，在函数执行上下文的时候变量对象(variable object)是无法直接访问的，所以由AO扮演VO角色，包括arguments属性等。
3.  创建作用域链，作用域链 = 活动对象(AO) + [[scope]]，[[scope]]为function的一个内部属性，与function的创建方式与所在代码位置相关。
4.  变量的初始化。这个就简单的理解了，在函数运行时对一些变量进行赋值等一些操作。

### 我们所了解的闭包
`E.g 1：`

```
function foo(){
    var x = 0;
    return function(){
        alert(x++);
    }  
}
var retFunc = foo();
//foo内部返回一个function 可以随意访问foo的内部变量 x 
retFunc (); // 0
retFunc (); // 1
var x = 0;
retFunc (); // 2
```

这是我们最常见的使用闭包特性来进行的处理。此处的 x 是从返回的内部函数的[[scope]]上搜索而来的，静态作用域。 再来一个例子可能可以更鲜明一点的理解静态的含义 `E.g 2：`

```
var x = 0;
function foo(){
    alert(x);
}
(function(){
    var x = 1;
    foo();
})();// 弹出 0
```

静态就是在此处并不弹出0的原因，因为每个函数的[[scope]]均是在函数定义的时候就已经确定，并且不再修改了的，知道与函数一起销毁。 foo在定义的时候[[scope]]中就为全局变量的 x ，而在哪里调用是不会再影响foo的[[scope]]的。 我们最常遇到的闭包带来的问题就是循环中的闭包影响(更多的时候是在操作DOM节点的时候的循环绑定事件，并且还是用的循环中的循环数值作为参数)： `E.g 3：`

```
var data =  [];
    for(var i =0;i<3;i++){
        data[i] = function(){
        alert(i);
    }
}
```

当我们在调用data[1]()，data[2]()，data[3]()时均会返回 3。因为data数组中的每个函数的[[scope]]都存放着全局的 i，当在调用的时候 i 已经在循环中被赋值为3，故而不管调用哪一个方法，均会从该方法的[[scope]]中查找 i，便是目前的全局i，值为3。 解决办法便是在多制造出一层函数。(在[[scope]]中后进入的优先级更高，从栈首开始搜索。) `E.g 4：`

```
var data =  [];
for(var i =0;i<3;i++){
    data[i] = (function(x){
        return function(){
            alert(x);// 此处便有所不同
            alert(i); // 此处仍是3
        }
    })(i);
}
```

使得每次alert的值并不是从[[scope]]中查询全局的i值。
### 闭包的原理
闭包是代码块和创建该代码块的上下文中数据的结合。技术上来说创建函数的父级上下文中的数据是完全保持在该函数的内部属性[[scope]]中的，这也是的我们的内部函数能够访问外部函数的自由变量。所以理论上来说，所有函数都可以当做闭包。 闭包实质上是通过函数内部的[[scope]]属性来实现的。[[scope]]是所有父变量对象的层级链，处于当前函数上下文之上，在函数创建时存于其中。 [[scope]]在函数创建时被存储－－静态（不变的）直至函数销毁。即：函数可以永不调用，但[[scope]]属性已经写入，并存储在函数对象中。
### 我所认为的闭包
闭包不简单，也不复杂。在JS中纯粹只是JS实现机制的一种产物，为我们所利用。理解作用域，作用域链，[[scope]]等JS知识，闭包自然而然的就会有了一定了解。本文如有错误，请各位矫正，谢谢~ 才疏学浅，无法将闭包讲的更深入，具体可以参考一下文章： http://www.cn-cuckoo.com/2007/08/01/understand-javascript-closures-72.html http://www.cnblogs.com/TomXu/archive/2012/01/31/2330252.html http://baike.baidu.com/view/648413.htm