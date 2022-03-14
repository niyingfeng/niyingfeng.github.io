---
title: 【译】 ECMAScript 6 模块化：最后的战役（上）
date: 2014-10-09
tags: 前端开发,学习与方案
---

原文地址：[http://www.2ality.com/2014/09/es6-modules-final.html](http://www.2ality.com/2014/09/es6-modules-final.html)
原文作者：Dr. Axel Rauschmayer

译者：倪颖峰
地址：[freefe.cc](http://freefe.cc)

在2014年7月底，TC39的另外一个会议，敲定了关于 ECMAScript 6 模块化语法的最后一些细节。本文完整的概述了关于 ES6 模块系统。

## 1. 当前 JavaScript 的模块系统。

JavaScript 目前没有内置方法来支持模块化，不过社区创造了非常不错的变通方案。两个重要的（可惜相互不兼容）标准是：

1. CommonJS 模块化：该标准主要被使用在 Node.js 中（Node.js 模块化中有一些超出 CommonJS 的语法的特性）。特点：语法简洁，为同步加载设计，主要使用在服务器端。
2. 异步模块定义（AMD）：该标准最流行的应用就是 RequireJS。	特点：略微复杂的语法使得 AMD 可以不使用 eval() 实现（或者说编译过程），为异步加载设计，主要在浏览器端使用。

以上只是对目前的状况简单粗暴的解释了一下，如果你想更深入的了解一下可以读一下 Addy Osmani 的“使用AMD，CommonJS 和 ES Harmony 写模块化JavaScript”。

## 2. ECMAScript 6 模块化

ECMAScript 6 模块化的目标是创建一种 CommonJS 和 AMD 使用者都乐意接受的方式：

1. 类似 CommonJS，拥有简洁的语法，倾向于单一的接口并且支持循环依赖。
2. 类似 AMD，直接支持异步加载和配置模块加载。

内置语言允许 ES6 模块化超出 CommonJS 和 AMD 规范（之后会详细介绍）：

1. 语法将比 CommonJS 的更简洁。
2. 结构可以做静态分析（静态检测，优化等）。
3. 比 CommonJS 做的更好的循环依赖。

ES6 模块化标准包括两部分：

1. 声明语法（引入与导出）。
2. 编程式加载接口：用来配置如何加载模块和按条件加载模块。

## 3. ES6 模块语法概述

有两种导出方式：命名式导出（每个模块可以多个）和定义式导出（每个模块仅一个）。

3.1. 命名式导出（每个模块可以多个）
    
一个模块可以通过前缀关键词 export 声明来导出多个。

这些导出以名字进行区分，称之为命名式导出。

```
// ----- lib.js ------
export const sqrt = Math.sqrt;
export function square( x ){
    return x*x;
}
export function diag( x, y ){
    return sqrt( square( x ) + square( y ) );
}

// -------- main.js -------
import{ square, diag } from 'lib';
console.log( square(11) ); // 121
console.log( diag( 4, 3 ) ); // 5
```

也有其他方式带定义命名式导出（稍后介绍），但是我发现此方式是最方便的：如果没有外层环境，你可以很简单的书写代码，然后以你想要的关键词来标识所有的东西。

如果需要，你也可以导入整一个模块，通过属性命名来指向命名式的导出：

```
import * as lib grom 'lib';
console.log( lib.square( 11 ) ); // 121
console.log( lib.diag( 4, 3 ) ); // 5
```

在 CommonJS 语法下的相同代码：有段时间我在 Node.js 下尝试了几种不错的策略来减少我的模块导出时代码冗余。现在我比较喜欢下面这种简单但是略微冗长的风格，有点类似于模块模式：

```
// ---- lib.js -----
var sqrt = Math.sqrt;
function square( x ){
    return x*x;
}
function diag( x, y ){
    return sqrt( square( x ) + square( y ) );
}

module.exports = {
    sqrt : sqrt,
    sqare : sqare,
    diag : diag
}

// ------ main.js ------
var square = require( 'lib' ).square;
var diag = require( 'lib' ).diag;
console.log( square( 11 ) ); // 121
console.log( diag( 4, 3 ) ); // 5
```

### 3.2. 定义式导出（每个模块仅一个）

在 Node.js 社区很受欢迎的模块导出单一值。当然在需要经常有构造函数和类来创建模型的前端开发中也类一样，一模块一模型。一个 ECMAScript 6 模块可以采用定义式导出，导出最主要的值。定义式导出容易引用。

以下为单函数的 ECMAScript 6 模块：

```
// ----- myFunc.js -------
export default function(){ };

// ------ main.js --------
import myFunc from 'myFunc';
myFunc();
```

定义式导出一个类的 ECMAScript 6 模块如下：

```
// ----- MyClass.js -------
export default class(){ };

// ------ main.js --------
import MyClass from 'MyClass';
let inst = new MyClass();
```

注：定义式导出声明的操作数是一个表达式，往往不需要名字。代替之的是通过模块的名称来确认。

### 3.3. 同一模块中使用命名式导出和定义式导出

一下模式在 JavaScript 中非常常见：一个库仅仅是单一的函数，不过通过该函数的属性来提供其他的支持。包括 jQuery 和Underscore.js。下面是简单的 Underscore 作为 CommonJS 模块的写法：

```
// ----------underscore.js ----------
var _ = function( obj ){};
var each = _.each = _forEach = function( obj, iterator, context ){}

module.esports = _;

// ---------- main.js ---------
var _ = require('underscore');
var each = _.each;
```

在ES6下，函数 _ 是定义式导出而 each 和 forEach 是命名式导出。 这证明事实上是可以同时进行命名式导出和定义式导出的。例如之前的 CommonJS 模块，以 ES6 来重写模块，如下：

```
// --------underscore.js--------------
export default function( obj ){ }
export function each( obj, itertor, context ){ }
export { each as forEach }

// --------main.js ------------
import _, { each } from 'underscore';
```

需要注意的是 CommonJS 版本和 ECMAScript 6 版本仅仅是大致相同。后者为扁平式结构，而前者为嵌套式结构。喜欢哪一种风格有自己决定，不过扁平式结构具有做静态分析的优势（下面会提到优点）。CommonJS 风格看起来为了满足对象的需要部分被作为命名空间，可以通过 ES6 模块来实现这种需求并且导出实现。

定义式导出仅仅是另一种形式的命名式导出。

定义式导出事实上是命名式导出使用了特殊名称 default 。即，下面两个是等价的：

```
import { default as foo } from 'lib';
import foo from 'lib';
```

类似的，下面两者也是等价的定义式导出：

```
// --------module1.js-----------
export default 123;
// --------module2.js-----------
const D = 123;
export { D as default };
```

那为何我们还需要命名式到导出呢？

你可能会疑虑，为何我们还需要命名式导出，而不是类似于 CommonJS 形式的单一定义式导出对象？答案就是，你不能通过对象来强制执行一个静态结构并且会丢失相关的优势（下一节描述）。
