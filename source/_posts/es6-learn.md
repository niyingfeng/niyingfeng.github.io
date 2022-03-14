---
title: ES6语法回顾
date: 2016-11-02
tags: ES6,JS
---

随着 Babel 在各类开源项目中的使用，ES6 已经进入日常生活。在此简单回顾一下 ES6 语法概念。 `箭头函数 =>`

```
var foo = function(){return 1+1;};
//等价于
let foo = () => 1+1;

var foo = function(a, b){
    if (a !== undefined && b !== undefined) {
        return a + b
    } else {
        return 0
    }
};
//等价于
var add = (a, b) => {
    if (a !== undefined && b !== undefined) {
        return a + b
    } else {
        return 0
    }
}
```

`let,const` 与var具有函数级别作用域不同，let和const不同具有块级作用域，let定义变量，const定义常量。

```
var a = 1;
{
  let a = 2;
  console.log(a); //2
}
console.log(a); //1
```

`解构赋值`

```
var value = [1, 2, 3, 4, 5];
var [el1, el2, el3] = value;

var value = [1, 2, [3, 4, 5]];
var [el1, el2, [el3, el4]] = value;

var person = {firstName: "John", lastName: "Doe"};
var {firstName, lastName} = person;

function findUser(userId, {includeProfile, includeHistory}) {
    if (includeProfile) ...
    if (includeHistory) ...
}
```

`增强对象字面量`

```
var worker = {
    company: 'freelancer',
    work() {
        console.log('working...');
    }
};
```

`参数默认值`

```
function findClosestShape( x=0, y=0 ){
    // ...
}
```

`余参`

```
function format( pattern, ...params ){
    // return params;
}
console.log( formart( 'a', 'b', 'c' ) );
```

`class` 增加类的概念

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

`扩展操作符`

```
let arr = [ -1, 7, 2 ];
let highest = Math.max( ...arr ); // 7
new Date( ...[ 2011, 11, 24 ] );
// 非破坏性的链接单个元素
let arr2 = [ ...arr, 9, -6 ]; // [ -1, 7, 2, 9, -6 ]
```

`for-of 循环（对于所有遵守 ES6 迭代规则的对象均有效）`

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

`模块化`

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

`模板字符串`

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