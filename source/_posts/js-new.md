---
title: JS中的 new 操作符简单理解
date: 2012-10-08
tags: new,构造函数
---

首先上一一个简单的 new 操作符实例

```
var Person = function(name){
    this.name = name;
    this.say = function(){
            return "I am " + this.name;
    };
}

var nyf = new Person("nyf");
nyf.say();
```

简单来说，上述例子中，以 new 操作符调用构造函数的时候，函数内部发生以下变化：

1.  创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
2.  属性和方法被加入到 this 引用的对象中。
3.  新创建的对象由 this 所引用，并且最后隐式的返回 this 。

以上情况在 new 操作符调用下，后台就相当于

```
var Person = function(name){
    //var this = {};
    this.name = name;
    this.say = function(){
        return "I am " + this.name;
    };
    //return this;
}
```

对于以上的讲述不知道有没有讲清楚。

```
var obj = new Base();
```

相当于运行以下代码

```
var obj = {};
obj.__proto__ = Base.prototype;
Base.call(obj);
```

对于ES5中添加了 Object.create()，

```
if(typeof Object.create !== "function"){
    Object.create = function(o){
        function F(){};
        F.prototype = o;
        return new F();
    }
}
```