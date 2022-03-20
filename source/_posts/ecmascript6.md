---
title: 【译】来试试 ECMAScript 6
date: 2014-08-11
tags: 
- ES
- ES6
---

原文地址：http://www.2ality.com/2014/08/es6-today.html 原文作者：Dr. Axel Rauschmayer （ 译者： 可以在此处了解 ES5.1 的情况 ） ECMAScript 6 （ES6）听起来依旧感觉离我们很远。毕竟，它要到2015年中旬才能成为标准。但是，它的一些特性逐渐出现在一些浏览器中，有其内置的编译器将 ES6编码 转化为 ES5编码。由于 ES6 的特性集为冻结状态，所以通过后者实现是一个不错的解决方案。 本文简述一下 ECMAScript 6 的特性以及介绍一下现在来使用它们的一些工具。
### 1. ECMAScript 6 的亮点
本段展现一些 ECMAScript 6 的亮点。

### 1.1. 新语法

`对象字面量 - 属性值简写（用于后面重构的例子）：`

```
```
let first = 'Jane';
let last = 'Doe'

let obj = { first, last };
// 等同于
let obj = { first:first, last:last }
```
```

`对象字面量 - 方法定义：`

```
```
let obj = {
        myMethod( arg0, arg1 ){
                // ...
        }
}
```
```

`箭头函数：`

```
```
let arr = [ 1,2,3 ];
let squares = arr.map( x => x\*x );
```
```

`扩展操作符：`

```
```
let arr = [ -1, 7, 2 ];
let highest = Math.max( ...arr ); // 7
new Date( ...[ 2011, 11, 24 ] );
// 非破坏性的链接单个元素
let arr2 = [ ...arr, 9, -6 ]; // [ -1, 7, 2, 9, -6 ]
```
```

`重构：`

```
```
let [ all, year, month, day ] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('20110-12-12');
let { first, last } = { first: 'Jane', last: 'Doe' }
```
```

`参数默认值`

```
```
function findClosestShape( x=0, y=0 ){
        // ...
}
```
```

`余参`

```
```
function format( pattern, ...params ){
        // return params;
}
console.log( formart( 'a', 'b', 'c' ) );
```
```

`通过重构命名参数：`

```
```
class Entries{
        // ...
        selectEntries( { from=0, to=this.length } = {} ){
                // Long: { from: from=0, to: to=this.length }
                // Use 'from' and 'to'
        }
}

let entries = new Entriea();
entries.selectEntries( { from:5, to: 15 } );
entries.selectEntries( { from:5 } );
entries.selectEntries( { to: 15 } );
entries.selectEntries( { } );
entries.selectEntries();
```
```

`模板字符串：`

```
```
let str = String.raw`This is a text 
with multiple lines.
 Escapes are not interpreted,
 \n is not a newline.`

var parts = '/2012/10/Page.html'.match(XRegExp.rx`
                ^ # match at start of string only
                / (?<year> [^/]+ ) # capture top dir name as year
                / (?<month> [^/]+ ) # capture subdir name as month
                / (?<title> [^/]+ ) # capture base name as title
                \.html? $ # .htm or .html file ext at end of path
        `);
console.log( parts.year ); // 2012
```
```

`类：`

```
```
class Person {
        constructor( nane ){
                this.name = name;
        }
        describe(){
                return "Person called" + this.name;
        }
}
// 子类
class Employee extends Person{
        constructor ( name, title ){
                // super.constructor( name )
                super( name );
                this.title = title; 
        }
        describe(){
                // super.describe()
                return super() + "(" + this.title + ")";
        }
}
```
```

`内置函数的子类，如 Error 和 Array：`

```
```
class MyError extends Error{
        // ...
}
```
```

`for-of 循环（对于所有遵守 ES6 迭代规则的对象均有效）`

```
```
let arr = [ 'foo', 'bar', 'baz' ];

for( let element of arr ){
        console.log( element );
}
// foo
// bar
// baz

for( let [ index, element ] of arr.entries() ){
        console.log( index + "." + element );
}
// foo
// bar
// baz
```
```

`模块化`

```
```
// lib.js
export const sqet = Math.sqrt;
export function square( x ){
        return x * x ;
}
export function diag( x, y ){
        return sqrt( square(x) + square( y ) );
}

// main.js
import { square, diag } from 'lib';
console.log( square( 11 ) ); // 121
console.log( diag( 4, 3 ) ); // 5
```
```

### 1.2. 标准库中的新功能

`Object.assign() ：`

```
```
class Point {
        constructor( x, y ){
                Object.assign( this, { x, y } );
                // ES6中 { x, y } 为 { x : x, y : y } 缩写。
        }
}
```
```

`Array.prototype.findIndex()：`

```
```
[ 6, 8, -5 ].findIndex( x => x<0 ); // 2
[ 6, 8, -5 ].findIndex( x => x<0 ); // -1
```
```

`Array.prototype.fill()：`

```
```
[ 'a', 'b', 'c' ].fill( 7 ); // [7, 7, 7]
new Array( 3 ).fill( 7 ); // [7, 7, 7]
```
```

`新字符串方法：`

```
```
'hello world'.startsWith( 'hello' ); // true
'*'.repeart( 5 ); // *****
```
```

`Map（值可以为任何数据格式）：`

```
```
let obj = {};
let map = new Map();

map.set( obj, 123 );
map.get( obj ); // 123
map.has( obj ); // true
map.delete( obj ); // true
map.has( obj ); // false
```
```

`Set：`

```
```
let arr = [5, 1, 7, 7, 5];
let unique = [ ...new Set( arr ) ]; // [ 5, 1, 7 ]
```
```

### 1.3. 更多 ECMAScript 6 概要

ECMAScript 6：JavaScript 的下一步棋 es6 特性 https://github.com/lukehoban/es6features#readme es6 利刃 https://github.com/hemanth/paws-on-es6
### 2. 现在就是用 ECMAScript 6
ECMAScript 6 特性开始不断在各种引擎中出现。你可以预览一下 Kangax 的” ECMAScript 6 兼容性表“来知道各个引擎的支持情况。 但对于现实中的项目，你还需要借助一些工具来使当前引擎使用 ECMAScript 6： "es6-tools"（https://github.com/addyosmani/es6-tools） ：就是这样一个完整的工具列表。 "ES.next showcase" （https://github.com/sindresorhus/esnext-showcase） ：展示了实际使用 ECMAScript 6 的特性。两个实例： 下一版本的 Ember.js 将通过 ES6 的模块 Transplier 来支持 ES6 模块。 下一个版本的 ArgularJS 将通过 Traceur 来支持 ES6 模块。 以下将介绍一些 ES6 应用的工具。
### 3. ECMAScript 6 编译器
如果一个工具需要将 ECMAScript 6 代码转换到 ECMAScript 5，那么它的功能需要超越转换器，所以称之为编译器。两个重要的编译器是 TypeScript 和 Traceur。

### 3.1. TypeScript

跟踪 ECMAScript 6 代码时 TypeScript 开发者所宣称的目标。因此，该语言允许你为 ECMAScript 6 添加注释（为可选）。 TypeScript 可以通过 npm 方便的安装，IDEs Visual Studio 和 WebStorm 均被支持。 当前的 TypeScript 的模块语法有一点滞后于 ECMAScript 6 的规范。它支持两种模块标准：CJS（Node.js） 和 AMD（RequireJS）。

### 3.2. Traceur

Traceur 是最流行的纯 ECMAScript 6 编译器。它对于新特性的支持令人震撼。Traceur 的开发者将它读为 ' tray-SOOR '。有两种方式来使用 Traceur。 静态形式：构建工具（如 Grunt，Gulp，Broccoli 等）的 Traceur 插件可以在开发时将 ES6 文件自动编译为 ES5 文件。查看 es6 工具详情（https://github.com/addyosmani/es6-tools） 动态形式：如果你的 web 应用里有 Traceur 那么你可以通过给 script 标签赋值 type 属性为 module 进行 ES6 编译。

```
```
<div id="output"></div> 
<script src="https://google.github.io/traceur-compiler/bin/traceur.js"></script> 
<script src="https://google.github.io/traceur-compiler/src/bootstrap.js"></script> 
<script type="module"> 
        var output = document.getElementById('output'); 
        var w = 'world'; 
        output.textContent = `Hello ${w}!`; 
</script>
```
```

你可以通过传递一个编译参数来使 Traceur 使用确定的模块标准： 1. ES6 的模块加载API 2. AMD（RequireJS） 3. CJS（Node.js）
### 4. 模块系统 以及 ECMAScript 6
一些现有的和新的 JavaScript 模块系统都通过现成的或者插件的方式来支持 ECMAScript 6。 1. AMD 和 CJS：ES 6 模块编译器（https://github.com/square/es6-module-transpiler）加入了将 ECMAScript 6 模块语法转移为 ECMAScript 5 将其编译为 AMD 或者 CJS。极简风格的解决方案较为吸引人。 2. Browserify：通过基于 Traceur 的 es6ify（https://github.com/thlorenz/es6ify） 转换来支持 ES6。 3. webpack（https://github.com/webpack/webpack）来自现有的 ECMAScript 6 支持。 4. ES6 模块加载补充：基于 ES6 API 和 在Node.js和当前浏览器中动态加载 ES6 模块。以下面两个工具进行补充： SystemJS：基于 ES6 的模块加载器，除了加载 ES6 模块之外还加载 AMD 和 CJS 模块。 jspm.io：为 SystemJS 而出现的包管理器。
### 5. ECMAScript 6 命令行
JavaScript 命令行是测试特性常用的交互方式。这边简单介绍下可以是使用 ECMAScript 6 语法的命令行。

### 5.1. ES6 Fiddle

Jeff 编写的 ES6 Fiddle（http://www.es6fiddle.com/）是一款基于 Traceur 的 ECMAScript 6 命令行工具。你可以点击工具栏的一个图标来通过 URL 和唯一的 ID 来保存一个实例。

### 5.2. Traceur 转码 demo

Traceur 有它自己的 demo 交互页面（http://google.github.io/traceur-compiler/demo/repl.html）。这是一些页面的提示： 1. 你可以打开控制台，通过 console.log() 看到所有你输入到控制台的 ES6 源码。 2. ES6 的源码都被添加到页面的 URL 中，所以你可以通过分享 URL 来分享 ES6 实例。 3. Traceur 在 ES5 中实现了许多 ES6 的方法，意味着你可以在控制台中直接使用例如 Array.form() 之类的方法。 4. let 和 const 变量声明仍在实验阶段，可以通过参数来打开。但是生成的代码较多。幸好这些计划（https://github.com/google/traceur-compiler/issues/6）极大的提高了对 let 和 const 的支持：比如，Traceur 可以用多种方式将 let 转到 var ，这样就不会有较大耗损性的使用let（前瞻性的）。

### 6. ECMAScript 6 shims

Shims 是一些将未来系统属性模拟到当前系统中的一些库。ECMAScript 6 标准库中包含有趣的新功能，这些功能一般都可以通过库来反向编译为 ECMAScript 5。 1. es6-shim（https://github.com/paulmillr/es6-shim）：支持多种 ECMAScript 6 标准库中的特性。 2. ECMAScript 6 promises 的 Shims（Traceur 有它自己的 promise polyfill）： RSVP.js（https://github.com/tildeio/rsvp.js/）：是 ES6 API 的一个超集。 es6-promise（https://github.com/jakearchibald/es6-promise）是 RSVP.js 的子集，仅包含ES6 API。 Q.Promise 与 ES 6 兼容。
### 7. 那么继续学习 ECMAScript 5 还有意义么？
众所周知，你现在已经完全可以使用 ECMAScript 6 的代码来避免旧版本的 JavaScript。那是否就意味着我们再也不需要学习 ECMAScript 5了？当然不是，举几个原因： 1. ECMAScript 6 是 ECMAScript 5 的一个超集，新版本 JavaScript 绝对不能破坏旧版本代码。那么你学习 ECMAScript 5并不会白费。 2. 有一部分 ECMAScript 6 特性是替换 ECMAScript 5 的特性的，但是前提是依旧可以使用。两个例子： classes 在内部转为构造函数，method 仍然为函数（因为一直都是）。 3. 只要 ECMAScript 6 被转义为 ECMAScript 5，那么了解编译过程的输出结果还是有用的。你必须要在一段时间内编译为 ES5（或许是几年），直到你可以在所有相关的浏览器中可以依赖 ES6，如现在依靠 ES5 一样。 4. 理解传统代码依旧是很重要的。