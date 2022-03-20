---
title: javascript 中的 this 关键词
date: 2012-10-09
tags: 
- this
---

this 关键词与面向对象编程有密切的关系，一直以来感觉 this 这东西很玄乎，很难把握其根本，从而导致了经常性的对 this 关键词进行对象查找的时候引发一些问题或者BUG。 其实当我们理顺思路，对于 this 的判断还是很简单的。 this 在 javascript 中只会存在于函数中（如在全局作用域中，则指向 Global 对象，即浏览器中的 window 对象），其只有4中函数调用方式： `1.Function Invocation Pattern（纯粹的函数调用）` 当在全局中生命一个 function 并且调用它，那么函数中的 this 指向 window。

```
function a(){
    this.b = "c";
    console.log(this === window);// true
}
a();
console.log(b);// "c"
```

`2.Method Invocation Pattern（作为方法调用）` 当作为一个方法调用的时候，即某一被调用函数被作为某个对象的属性，this指向调用该方法的对象。

```
var a ={};
a.b = function(){
    this.x = 1;
    console.log(this === a);
}
a.b();// true
a.x; // 1
```

`3.Constructor Pattern（构造函数式调用）` 使用 new 操作符来进行函数的调用，this 指向新创建的对象。

```
function a(){
    this.x = 1;
}
var b = new a();
b.x;// 1
```

`4.Apply Pattern（apply 调用）` 使用内置的 call 和 apply 来进行调用，那函数内部的 this 将指向其第一个参数。

```
function a(){
    this.x = 1;
}
var b = {}
a.apply(b);
b.x;// 1
```

当我们明白对于 this 的4中其函数调用方式，便可以很明白的 this 指向的对象。 如果想要深入的了解js中this的信息的 可以看大叔的www.cnblogs.com/tomxu/archive/2012/01/17/2310479.html