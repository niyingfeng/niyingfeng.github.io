---
title: 【译】 JavaScript 之 this：看它为何让你斩不断理还乱
date: 2014-05-15
tags: JS,This
---

原文地址：[http://www.2ality.com/2014/05/this.html](http://www.2ality.com/2014/05/this.html)
原文作者：Dr. Axel Rauschmayer

在JavaScript中，特殊变量 this 相对来说较为复杂，因为它不仅仅只在面向对象设定中出现，其随处可见。这里会解释一下 this 的工作原理以及会引起问题的地方，最佳实践总结。

理解 this，最好的方式是区分被使用的三种类型的位置：

- 函数内部：this 是一个额外的隐式的参数。
- 函数外（顶层作用域）：this 在浏览器中指向全局对象，Node.js中指向一个模块的暴露接口。
- 在传递给 eval() 的字符串中：eval() 也许会取得当前 this 的值，或者是将其设置为全局对象，取决于是直接或者间接的调用。

来看一下各个类型。

## 1. 在函数内部的 this

这是 this 一种最常用的方式，因为在 JavaScript 中，函数以三种不同的角色代表了所有的可调用的结构形式。

1. 确切的函数（this 在松散模式中为全局对象，在严格模式中为 undefined ）
2. 构造函数（this 指向新创建的实例）
3. 方法（this 指向方法调用的接受者）

在函数中，this 被常常认为是一个额外的隐式的参数。

### 1.1 在确切的函数中的 this

在确切的函数中，this 的值依赖于其所在的模式：

**松散模式：this 指向全局对象（浏览器下为windows）**

```
function sloppyFunc(){
    console.log( this === window )
}
sloppyFunc();
```

**严格模式：this 的值为 undefined**

```
function strictFunc(){
    "use strict";
    console.log( this === undefined );
}
strictFunc();
```

就是说，this 是一个隐式的，设置有默认值（window 或者 undefined）的参数。但是，你可以使一个函数通过 call() 或者 apply() 调用来明确的指定 this 的值。

```
function func( arg1, arg2 ){
    console.log( this );
    console.log( arg1 );
    console.log( arg2 );
}
func.call( { a:'a' } , 'b', 'c' ); 
// Object {a: "a"}
// b
// c
func.apply( { a:'a' } , [ 'b', 'c' ]); 
// Object {a: "a"}
// b
```

### 1.2 在构造函数中的 this

你通过 new 操作符来调用的函数即是构造函数，该操作符创建一个新的对象，并把它通过 this 传递给构造函数：

```
var savedThis;
function Constr(){
    savedThis = this;
}

var inst = new Constr();
console.log( savedThis === inst );
```

new 操作符使用 JavaScript 实现大致如下（一个更为准确复杂的实现 http://speakingjs.com/es5/ch17.html#_the_new_operator_implemented_in_javascript）：

```
function newOperator( Constr, arrayWithArgs ){
    var thisValue = Object.creat( Constr.prototype );
    Constr.apply( thisValue, arrayWithArgs );
    return thisValue;
}
```

### 1.3 方法中的 this

在方法中，所有的事情都和传统的面向对象语言类似： this 指向接受者，即方法被调用的那个对象。

```
var obj = {
    method : function(){
        console.log( this === obj );
    }
}
obj.method();
```

## 2. 顶级作用域的 this
    
在浏览器环境中，顶级作用域是全局作用域， this 指向全局作用域（诸如 window 之类）：

```
<script>
    console.log( this === window ); // true
</script>
```

在 Node.js 中，你基本在模块中编写代码。那么当前顶级作用局就是一个特定的模块作用域。

```
// （在 Node.js 的具体模块中）
// 'global'（非 window） 指向全局对象
console.log( Math === global.Math ); // true

// 'this' 并不指向全局对象
console.log( this !== global ); // true
// 'this' 指向模块的暴露接口
console.log( this === module.exports );
```

## 3. eval() 中的 this

eval() 既可以被直接调用（通过一个确切的方法调用）也可以被间接调用（通过另一些方式）。以下是具体解释。

如果 eavl() 被间接调用， this 指向全局对象：

```
( 0, eval )( " this === window" );
```

否者，如果 eval() 被直接调用，那么 this 指向 eval() 所处的执行环境。例如：

```
function sloppyFunc(){
    console.log( eval("this") === window );
}
sloppyFunc(); // true

function strictFunc(){
    "use strict";
    console.log( eval("this") === undefined );
}
strictFunc(); // true


var savedThis;
function Constr(){
    savedThis =eval(" this");
}

var inst = new Constr();
console.log( savedThis === inst ); // true


var obj = {
    method : function(){
        console.log( eval("this") === obj );
    }
}
obj.method();
```

## 4. this 的相关陷阱

这里有3个值得注意的和 this 有关的陷阱。 记住，严格模式可以使得每种情况变得正常，因为在确切的函数中 this 都会为 undefined ，当出错的时候会得到警告。

### 4.1 忘记 new

如果你调用构造函数却忘记了 new 操作符，那么你会意外的调用一个实实在在的函数。故而， this 就不会指向当前值。在松散模式中，this 指向window，并且会创建全局变量。

```
function Point( x, y ){
    this.x = x;
    this.y = y;
}

var p = Point( 7, 5 ); // 忘记 new 操作符
console.log( p === undefined ); // true

//创建全局变量
console.log( x ); // 7
console.log( y );
```

幸亏有严格模式，你可以获取到警告（this === undefined）

```
function Point( x, y ){
    "use strict";
    this.x = x;
    this.y = y;
}

var p = Point( 7, 5 );
```

### 4.2 获取方法不当

如果你获取方法的值（而非调用），你会将方法又转变为函数。结果值的调用为函数的调用而非方法的调用。这种获取方式发生在你将一个方法作为参数传递给一个函数或者方法的时候。实际环境中的例子包括 setTimeout() 和 时间注册处理程序。这边会使用函数 callIt() 来同时模拟用例。

```
/* 与 setTimeout() 和 setImmediate() 类似 */
function callIt( func ){
    func();
}
```

如果你调用一个松散模式下的函数， this 指向全局对象，并且创建全局变量：

```
var counter = {
    count : 0,
    inc : function(){
        this.count++;
    } 
}
callIt( counter.inc );

console.log( counter.inc ); // 0, 无效
console.log( count );
```

如果你调用严格模式下的函数，this 为undefined， 代码也将会无效。但是会得到一个警告。

```
var counter = {
    count : 0,
    inc : function(){
        "use strict";
        this.count++;
    } 
}
callIt( counter.inc );
```

可以使用 bind() 开解决：

```
var counter = {
    count : 0,
    inc : function(){
        "use strict";
        this.count++;
    } 
}
callIt( counter.inc.bind( counter ) ); 
console.log( counter.count );
```

bind() 创建一个接受的 this 值为 counter 的新函数。

### 4.3 this 跟踪

当你使用一个确实的函数来代替方法，很容易忘记前者是有自己的 this值（尽管经常不使用）。因此你无法将前者的 this 指向方法的 this，由于它是跟踪状态的。看一下出错的实例：

```
var obj = {
    name : 'Jane',
    friends : [ 'Tarzan', 'Cheeta'],
    loop : function(){
        'use strict';
        this.friends.forEach(function( friend ){
            console.log( this.name + ' knows ' + friend );
        });
    }
}
obj.loop(); // TypeError: Cannot read property 'name' of undefined
```

在上面例子中，this.name 获取出错，原因是函数的 this为undefined，他与方法 loop() 内的 this 不一样，来看看3中解决方案。

**解决方案1： that = this。**将 this 设置给一个变量，使其不再跟踪状态（另一个常用的变量名为 self）再使用它。

```
loop : function(){
        'use strict';
        var that = this;
        this.friends.forEach(function( friend ){
            console.log( that.name + ' knows ' + friend );
        });
    }
```

**解决方案2：bind()。**使用 bind() 来创建一个 this 常指向当前值的函数（下面实例方法的 this）。

```
loop : function(){
        'use strict';
        this.friends.forEach(function( friend ){
            console.log( this.name + ' knows ' + friend );
        }.bind( this ));
    }
```

**解决方案3：forEach 的第二个参数。**该方法有第二个参数用来传递回掉函数在某一目标上进行调用。

```
loop : function(){
        'use strict';
        this.friends.forEach(function( friend ){
            console.log( this.name + ' knows ' + friend );
        }, this );
    }
```

## 5. 最佳实践
    
概念上来说，我认为确实的函数并不具有它们自己的 this，认为上述解决方案只是保持这个错觉。ECMAScript 6通过箭头函数来支持 this 的方法，函数没有其自己的 this 值。使用这些函数，你可以无忧无虑的使用 this，因为不会跟踪。

```
loop : function(){
        'use strict';
        // forEach 的参数就是箭头函数
        this.friends.forEach( friend =>{
            // 'this' 就是 loop 的 'this'
            console.log( this.name + ' knows ' + friend );
        });
    }
```

我不喜欢 APIs 中像一些普通函数的参数那样使用 this：

```
beforeEach(function(){
    this.addMatchers({
        toBeInRange : function(){ ... }
    });
});
```

箭头函数将一个隐式的参数转变为显式的，使得行为更为明确和兼容。

```
beforeEach( api => {
    api.addMatchers({
        toBeInRange : function(){ ... }
    });
});
```