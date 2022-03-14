---
title: 【译】ECMAScript 5 与 6 中方法的调度调用和和直接调用
date: 2014-07-29
tags: apply,call
---

原文地址：[http://www.2ality.com/2014/07/method-calls.html](http://www.2ality.com/2014/07/method-calls.html)

原文作者：Dr. Axel Rauschmayer

在 JavaScript 中有两种方式调用方法：通过调度调用（如：obj.someMethod( arg0, arg1 )）和直接调用（如：someFunc.call( thisValue, arg0, arg1 )）。本文解释一下两者的运行方式，以及为何在 ECMScript 6 我们较少的使用直接调用方法形式。

## 1. 调度式方法调用 vs 直接式方法调用

### 1.1. 基础：原型链

记住任何在 JavaScript 中对象事实上都是一个或者多个对象组成的链。第一个对象继承后者对象的属性。例如，一个数组 ['a', 'b']的原型链看起来应该为如下形式：

1. 实例，保存着元素 'a' 和 'b'
2. Array.prototype，由 Array 构造函数提供的属性
3. Object.prototype，由 Object 构造函数提供的属性
4. null，（链的末端，并不是真正意义上链的成员）

你可以通过 Object.getPrototypeOf() 来检测原型链：

```
var arr = [ 'a', 'b' ];
var p = Object.getPrototypeOf;

p( arr ) === Array.prototype; // true
p( p( arr )) === Object.prototype; // true
p( p( p( arr )));
```

处在更靠前对象中的属性会覆盖靠后对象中的属性。例如，Array.prototype 中提供的一个数组形式版本的 toString() 方法覆盖了 `Object.prototype.toString()`。

```
var arr = [ 'a', 'b' ];
Object.getOwnPrototypeNames( Array.prototype ); // ['toString', 'join', ...]
arr.toString();
```

### 1.2. 调度式方法调用

如果仔细观察 arr.toString() 的方法调用，可以发现它实际上执行了两步：

1. 调度：在 arr 的原型链上，取出第一个属性名为 toString 的属性值。
2. 调用：调用所取到的值，设置隐式参数 this 为接受者 arr 作为其方法调用。

你可以通过函数的 call() 方法来展示这两步：

```
var func = arr.toString; // 调度
func.call( arr ); // 直接调用，提供一个明确的 this 值
```

### 1.3. 直接式方法调用

在 JavaScript 中有两种形式的直接式方法调用：

1. Function.prototype.call( thisValue, arg0?, arg1?, ... )
2. Function.prototype.apply( thisValue, argArray? )

call 和 apply 两种方法均在函数上进行调用。这两种方式和一般的函数调用不一样，需要指定 this 的值。call 通过单个参数形式给调用函数提供参数，apply 通过数组的方式提供。

通过动态调度式调用方法有一个问题就是该方法必须在一个对象的原型链上。call() 可以让你指定接受者来直接调用一个方法。这意味着你可以从一个对象中借来一个方法而并不需要存在当前原型链上的。举个栗子：你可以借用Object.prototype.toString 从而使 arr 调用原生的，未被重写的 toString()。

```
Object.prototype.toString.call( arr );
```

方法在一个不同的对象（不只是继承与它们的构造函数）下调用称为通用。说起 JavaScript 中有一系列方法均是通用的。这列表包括许多数组的方法和所有的 Object.prototype 上的方法（它与所有的对象一起工作，从而使隐式的通用）。

## 2. 使用直接式方法调用的实例

### 2.1. 通过数组形式给方法传参

一些方法接受多个参数，但是每个参数只有一个值。那么你时候希望以数组形式进行传参呢？

比如，push() 允许你具有破坏性的将一些值添加到数组中：

```
var arr = ['a', 'b'];
arr.push( 'c', 'd' ); // 4
arr; // ['a', 'b', 'c', 'd']
```

但是你不能破坏性的添加一整个数组。你可以通过 apply() 来解决这个限制问题：

```
var arr = ['a', 'b'];
Array.prototype.push.apply( arr, [ 'c', 'd' ] ); // 4
arr; //  ['a', 'b', 'c', 'd']
```

类似的，Math.max() 和 Math.min() 也只能使用单个值：

```
Math.max( -1, 7 ,2 ); // 7
```

通过apply()，你可以对他们使用数组：

```
Math.max.apply( null, [ -1, 7, 2 ] ); // 7
```

### 2.2. 一个类数组对象转换成一个数组

在 JavaScript 中一些对象为类数组，它们大致上是数组，但是没有任何数组方法。看下两个例子。

首先，函数的特殊变量 arguments 是类数组， 它有一个 length 和 可以下标访问元素。

```
var args = function(){ return arguments; }( 'a', 'b' );
args.length; // 2
args[0]; // 'a'
```

但是 arguments 并不是 Array 的一个实例，并且没有 forEach() 方法。

```
args instanceof Array; // false
args.forEach; // undefined
```

其次，DOM 方法 document.querySelectorAll() 返回的 NodeList 实例。

```
document.querySelectorAll('a[href]') instanceof NodeList; // true
document.querySelectorAll('a[href]').forEach; // undefined
```

再次，一些复杂操作，你需要现将类数组转化成数组形式。这可以通过 Array.prototype.slice() 来实现。该方法将接受者中的元素拷贝出来放置到新的素组中：

```
var arr = ['a', 'b'];
arr.slice(); // ['a', 'b']
arr.slice() === arr; // fasle
```

如果直接式调用 slice()，可以将一个 NodeList 转化为一个数组 ：

```
var domList = document.querySelectorAll( 'a[href]' );
var links = Array.prototype.slice.call( domList );
links.forEach(function( link ){
    console.log( link );
});
```

也可以将 arguments 转化为数组：

```
function format(pattern) {
    var params = Array.prototype.slice.call(arguments, 1);
    return params; 
}
console.log(format('a', 'b', 'c')); // ['b', 'c']
```

### 2.3. 正确是使用 hasOwnPrototype() 

obj.hasOwnPrototype('prop') 可以检测对象 obj 是否有自己（非继承的）的 prop 属性。

```
var obj = { prop: 123 };
obj.hasOwnProperty('prop'); // true 
'toString' in obj; // true 
obj.hasOwnProperty('toString'); // fasle
```

然而，如果 Object.prototype.hasOwnPrototype 被覆盖过， 那么通过正确的调度式调用 hasOwnPrototype 可能回发生错误。

```
var obj = { hasOwnprototype : 123 };
obj.hasOwnPrototype( 'toString' );
// TypeError: number is not a function
```

如果一个对象的原型链上没有Object.prototype，那么通过调度式调用 hasOwnPrototype 可能出错。

```
var obj = Object.create( null );
obj.hasOwnPrototype( 'toString' );
// TypeError: undefined is not a function
```

两者，可以使用直接式调用 hasOwnPrototype 的方法来解决

```
var obj1 = { hasOwnProperty: 123 };
Object.prototype.hasOwnProperty.call(obj1, 'hasOwnProperty') ; // true 
   
var obj2 = Object.create(null);
Object.prototype.hasOwnProperty.call(obj2, 'toString') ; // false
```

### 2.4. 避免中间对象

对字符串应用一个数组的方法，比如 join() 转化，一般有两步：

```
var str = 'abc';
var arr = str.split(''); // step 1
var joined = arr.join('-'); // step 2
console.log( joined ); // a-b-c
```

字符串是类数组，可以作为数组通用方法的 this 值：
因此，直接式的调用可以避免第一步：

```
var str = 'abc';
var joined = Array.prototype.join.call( str, '-' );
```

类似的，你可以对字符串分割为数组后，或者通过直接式方法调用来使用 map()。

```
function toUpper( x ){
    return x.tiUpperCase();
} 

'abc'.split('').map( toUpper ); // [ 'A', 'B', 'C' ]
Array.prototype.map.call( 'abc', toUpper ); // [ 'A', 'B', 'C' ]
```

记住，直接式调用可能可以更高效，但是形式是不为优雅。但那是值得的。


## 3.  Object.prototype 和 Array.prototype 简短形式

你可以通过一个空对象字面量（其原型是 Object.prototype）来访问 Object.prototype 的方法。如下面两种直接式方法调用是等价的：

```
Object.prototype.hasOwnPrototype.call( obj, 'propKey' );
{}.hasOwnPrototype.call( obj, 'propKey' );
```

对于 Array.prototype 使用相同方式：

```
Array.prototype.slice.call( arguments );
[].slice.call( arguments );
```

这种形式变得颇为流行。相对于较长版本来说，可能没有更好的显示出作者的意图，但是它更为简短，速度方面也并没有很大的差距（译者注： [http://jsperf.com/array-prototype-slice-vs-slice2](http://jsperf.com/array-prototype-slice-vs-slice2) 和 [http://jsperf.com/object-prototype-tostring-call-vs-tostring](http://jsperf.com/object-prototype-tostring-call-vs-tostring) 显然字面量形式肯定会慢一点，要创建完对象或者数组之后再去原型上查询，但是差距并不是很大）。

## 4. 在 ECMAScript 6中 直接式调用的替代方案

感谢 ECMAScript 6 的新特性，你可以不再那么频繁的使用直接式方法调用了。

### 4.1.  扩展操作符 几乎可以替代 apply()
    
是我们用过 apply() 使用直接式方法调用的唯一原因是我们要转换一个数组为 arguments 显得比较麻烦，这也是 ECMAScript 6 有了扩展操作符（...）的原因。它为调度式调用方法提供了此功能。

```
Math.max( ...[ -1, 7, 2 ] ); // 7
```

另一个例子：

```
let arr = ['a', 'b'];
arr.push( ...['c', 'd'] ); // 4
arr; // ['a', 'b', 'c', 'd']
```

另一个边界==便捷， 扩展在 new 操作符中也是起作用的：

```
new Date( ...[ 2011, 11, 24 ] );
```

记住，apply() 是无法在 new 操作符中使用，上面的形式需要通过复杂的形式围绕 ECMAScript 5 来实现。

### 4.2. 在 ECMAScript 6 中类数组对象将不再成为累赘

一方面，  ECMAScript 6 拥有 Array.form() 方法，一个转化类数组对象为数组的简单方式。

```
let domLinks = document.querySelectorAll( 'a[href]' );
let links = Array.form( domLinks );
links.forEach(function( link ){
    console.log( link );
});
```

另一方面，你不在需要类数组 arguments，因为  ECMAScript 6 可以获取其余参数（三个点声明）：

```
function format( pattern, ...params ){
    return params;
}
console.log( format('a', 'b', 'c') ); // [ 'b', 'c' ]
```

### 4.3. hasOnPrototype()

hasOnPrototype()  基本是通过对象实现映射。幸好， ECMAScript 6 中有有了内置的 Map 数据结构，所以可以使你更少的使用 hasOnPrototype()。


### 4.4. 避免中间对象

Array.form() 可以式转换和映射一步完成，也可以在第二个参数传入回调函数。

```
Array.from('abc', ch => ch.toUpperCase())
// [ 'A', 'B', 'C' ] 
```

也可以作为两步：

```
'abc'.split('').map(function (x) { return x.toUpperCase() })
//[ 'A', 'B', 'C' ]
```
 