---
title: 关于 Generator Function 简单介绍
date: 2017-02-27
tags: ES6,function*,yield
---
## 基本概念

`Generator Function`（生成器函数），为一种可以从中退出，可以重新进入的，并且环境中的变量依旧会被保留的函数。粗略感觉有点类似于闭包存储的功能效果，不过`Generator Function`的使用场景不仅仅与此。

以下为 MDN 对 `Generator Function` 的大致描述：

> 调用一个生成器函数，并不会马上执行其主体方法，而是会生成返回一个该生成器函数的迭代器（Iterator）对象。当这个迭代器的 next 方法被调用时，生成器函数的主体会被执行到第一个 `yield` 表达式，该表达式定义了迭代器的返回值。或者被 `yield*` 委派至另一个生成器函数的迭代器对象。next 方法调用之后返回一个具有value属性 和一个done属性的对象，分别表示产出值和是否是最后的产出值。

简单的示例如下：

```
// 一个生成器函数
function* idFactory(){
    var id = 0;
    yield id++;
}

// 生成一个迭代器对象
var maker = idFactory();

// 调用迭代器对象的next方法，获得返回值对象 
maker.next(); // {value: 0, done: false}
// 第一个yield下去无代码执行
maker.next(); // {value: undefined, done: true}
```

## function\* & yield

`function*`为生成器函数的声明，而`yeild`则为生成器函数内部的关键词，作为该生成器函数产生的迭代器对象调用`next`方法时返回明确的期望的返回值，并从迭代器对象中暂停，之后某一时间仍能再次进入该迭代器，犹如打了个高级的断点的return。

或许你会疑惑，上面例子中，按期望来说，由于仅有一次`yeild`的执行，在第一次调用`next`后，返回值中便期望 done 为 true，却返回 false，并且在再次调用之后，done 才变为 true。

这块就需要对于`next`做简单的理解：

1. next 在遇到 yield 之后，暂定后续操作，便将 yield 后的表达式作为返回对象的 value 值，done 为 false
2. 再次调用 next，便往下执行，直到遇到下一个 yield
3. 如遇到 return，便将 return 的表达式作为返回对象的 value 值，done 为 true，迭代器关闭
4. 如往下执行中无 yield，也无 return，则返回 value 为 undefined，done 为 true，迭代器关闭
5. 对于已经关闭的迭代器，调用next，始终返回value 为 undefined，done 为 true

所以说，暂停之后，是无法去判断接下来是否还会运行到 yield，故而，第一个例子中，会在再次调用之后，明确此刻迭代器被关闭之后，done就有明确的返回值为true

```
// 一个生成器函数
function* idFactory(){
    var id = 0;
    yield id++;
    yield id++;
    return 2;
    yield id++;
}

// 生成一个迭代器对象
var maker = idFactory();

// 调用迭代器对象的next方法，获得返回值对象 
maker.next(); // {value: 0, done: false}
maker.next(); // {value: 1, done: false}
maker.next(); // {value: 2, done: true}
maker.next(); // {value: undefined, done: true}
```

若需要一个类似的无限迭代器，则需要暂停之后有所逻辑执行：

```
// 一个生成器函数
function* idFactory(){
    var id = 0;
    while(1) {
        yield id++;
    }
}

// 生成一个迭代器对象
var maker = idFactory();

// 调用迭代器对象的next方法，获得返回值对象 
maker.next(); // {value: 0, done: false}
maker.next(); // {value: 1, done: false}
maker.next(); // {value: 2, done: false}
```

## yield*

先来看一则小实例

```
function* idFactory(){
    var id = 0;
    yield id++;
    yield idFactory2();
    yield id++;
}
function* idFactory2(){
    yield 'X';
}

var maker = idFactory();
maker.next(); // {value: 0, done: false}
maker.next(); // {value: idFactory2, done: false}
maker.next(); // {value: 1, done: false}
```

迭代器在第二处的yield上，会返回一个迭代器，而非进入迭代器进行迭代，这边需要使用到`yield*`来进行处理，使得第二处yield进入内部的迭代器产生迭代。

```
function* idFactory(){
    var id = 0;
    yield id++;
    yield* idFactory2();
    yield id++;
}
function* idFactory2(){
    yield 'X';
}

var maker = idFactory();
maker.next(); // {value: 0, done: false}
maker.next(); // {value: 'X', done: false}
maker.next(); // {value: 1, done: false}
```

> 关于`yield*` 一个迭代对象，相当于在此处分别将该可迭代对象依次分开 yield 出去而已。并且除了生成器函数生成的迭代器可作为此处的迭代对象，包括数组，字符串等也可以作为迭代对象来进行使用。

```
function* idFactory(){
    var id = 0;
    yield id++;
    yield* "abc";
    yield* ['x','y'];
    yield id++;
}
var maker = idFactory();

maker.next(); // {value: 0, done: false}
maker.next(); // {value: "a", done: false}
maker.next(); // {value: "b", done: false}
maker.next(); // {value: "c", done: false}
maker.next(); // {value: "x", done: false}
maker.next(); // {value: "y", done: false}
maker.next(); // {value: 1, done: false}
```

唯一需要注意的是，由于 yield* 本身为表达式，其返回值便是可迭代对象迭代完毕（done为true）时的返回值。

```
function* idFactory(){
    var id = 0;
    yield id++;
    yield* idFactory2();
    yield id++;
}
function* idFactory2(){
    yield 'x';
    return 'y'
}
var maker = idFactory();

maker.next(); // {value: 0, done: false}
maker.next(); // {value: "x", done: false}
maker.next(); // {value: 1, done: false}
```

当 yield* 所迭代的迭代器，迭代完毕之后，此次 next 的调用会继续执行，而不是抛出 {value: 'y', done: true}，所以为以上所示。

## 总结

简单的来说，关于ES6中`Generator Function`有以下几个特征：

- 通过关键词 `function*` 进行声明，
- 调用之后并非执行其主体代码，而是返回该生成器函数的一个迭代器对象
- 迭代器对象通过`next`方法来进行迭代运行
- 迭代的返回值对象，每次运行以`yield`进行定义的返回值
- 对于生成器中的`yield*`来说，可以吧需要的yield的值委托给另一个新的迭代器或者可迭代的对象进行迭代。


## 相关阅读：

[MDN function*](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*)

[MDN yield*](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield*)
