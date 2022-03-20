---
title: 关于JavaScript的执行上下文
date: 2013-05-15
tags: 
- JS执行上下文
---

### 什么是执行上下文
执行上下文是 ECMAScript 中最常提到的概念。 规范上是这么解释的：

> When control is transferred to ECMAScript executable code, control is entering an execution context. Active execution contexts logically form a stack. The top execution context on this logical stack is the running execution context. 当控制器转入到ECMAScript可执行代码时，控制器就会进入一个执行上下文。激活的执行上下文形成一个逻辑上的堆栈。在这个逻辑堆栈的最上层的执行上下文就是目前运行中的执行上下文。

`e.g.1`

```
console.log(func);
function func(){}
var func;
console.log(func);
func = "abc";
console.log(func);
```

![](http://blog.chinaunix.net/attachment/201305/15/26672038_136859919084nK.png) 是的，他和执行上下文有关。 `e.g.2`

```
function show_arguments(a,b,c){
    console.log(a);
    console.log(b);
    console.log(c);

    console.log(arguments[0]);
    console.log(arguments[1]);
    console.log(arguments[2]);

    arguments[0] = 0;
    arguments[1] = 1;
    arguments[2] = 2;

    console.log(a);
    console.log(b);
    console.log(c);
}

show_arguments("a","b")
```

![](http://blog.chinaunix.net/attachment/201305/15/26672038_1368599382m21m.png) 是的，也和执行上下文有关。 `e.g.3`

```
var name = "I am window";
function show_name(){
    console.log(this.name);
}
var obj_test={
    name:"I am obj_test",
    wait_show:function(){
     setTimeout(show_name,100);
    }
}
show_name();
obj_test.wait_show();
```

![](http://blog.chinaunix.net/attachment/201305/15/26672038_136859973999R9.png) 这是一个之前在 this 中的例子，当然也与执行上下文有关。 执行上下文中东西非常的多。简单来说，执行上下文是有个抽象的对象，可以想象他是一块隐藏在JS控制器里面的内存，用来保存JS中的各种数据以便JS代码中使用这些数据，保证可以代码顺利执行。
### 执行上下文与其他 某某某 的关系
具体来说，一个执行上下文中包含了3部分， 变量对象，作用域链，this值。 ![](http://blog.chinaunix.net/attachment/201305/15/26672038_1368600265Ht0T.png) 这是一张很好的对执行上下文说明的图片，变量对象，作用域链，this值以及他们本身所包含的数据。可以理解为 EC在JS作用扮演着对JS代码执行控制的角色，其他所有的 变量啊 属性啊 函数啊都是隶属于他旗下的，由他进行把控。
### 执行上下文是什么时候产生的
不同的函数为什么会有不同的作用域，其本质就是因为他们在不同的执行上下文中，执行上下文当然不只是一个，当控制器转入到一段ECMA可执行代码的时候就会进入一个新的执行上下文。 `e.g.4`

```
function a(){
    ...//代码
    function b(){
        ...//代码
    }
    b();
}
a();
```

形式上比如当我调用一个外部函数a，（不是函数定义时，而是函数调用的时候）

> ->控制器转入外部a函数的代码时， ->激活函数 a的执行上下文 ECa，将ECa放入EC堆栈，ECa变为当前激活的执行上下文， ->进入a的代码并执行a中的代码， ->执行中。。。 ->遇到内部函数 b的调用， ->激活函数 b的执行上下文 ECb，将ECb放入EC堆栈，ECb变为当前激活的执行上下文， ->执行上下文堆栈 - ECb - ECa - ...... - Global EC ->进入b的代码并执行b中的代码， ->执行完b代码，退出当前执行环境 ECb，将ECb从执行上下文堆栈中推出，ECa又变为当前激活的执行上下文。 -> .......

理解了执行上下文与执行上下文堆栈的运行原理，那么理解一些其他东西，调用2次函数a其实并不是同一个EC。 `e.g.5`

```
function a(){
    var num = 0;
    return num++;
}

a();
a();
```

为什么两次调用a函数都是返回 1，而不是1,2。 用上下文堆栈的原理来解释是因为 在执行第一个函数a的时候，被激活的上下文为 ECa' 当执行完毕后就被推出堆栈。再执行第二个a的调用的时候，一个新的ECa'' 被激活放入上下文堆栈，ECa' 和 ECa''是相互独立的，num都会在VO阶段声明，AO阶段赋值为0。
### eval,Function的执行上下文
eval会触发一个calling context的东西。会使eval影响当前的执行上下文。 `e.g.6`

```
eval("var x = 10");
new Function("var z = 10");

(function(){
    eval("var y = 10");
    console.log(y);
})();

console.log(x);
console.log(y);
console.log(z);
```

![](http://blog.chinaunix.net/attachment/201305/15/26672038_1368611361BeEW.png) eval会产生自己的一个 evalContext，并且设置一个calling Context为激活它的 EC，然后菜eval中声明的变量等行为会影响到当前的EC。 虽然 z 那边直接被外部的 y 报错给打断了，其实 z 也是报错的，显然就相当于 Function 其实和一般的方法是一样的。
### 那么，执行上下文是什么？
那么，执行上下文是什么？那么，执行上下文是什么？那么，执行上下文是什么？那么，执行上下文是什么？那么，执行上下文是什么？。。。 只能说，它是一个抽象的对象，与JS代码执行进程有很大关系。包含 VO，SC，thisValue。 本文为个人了解观点，有不足之处望指出，谢谢~