---
title: jQuery 实现异步队列模块 Deferred
date: 2016-02-29
tags: jquery,jquery 源码,JS
---

jQuery 依赖 sizzle 选择器引擎，将 DOM 处理性能优化到了一个很极致的程度，,但 jQuery 还有另一个亮点的处理就是对于异步的处理封装，Deferred，异步队列模块。 异步队列模块最长用到的场景就是 jQuery 封装的 Ajax 模块，不过经常，我们是这么使用的

```
```
$.ajax({
        url: 'test.php',
        data: {test: 123},
        success: function(data, status, xhr){},
        error: function(xhr, status, error){},
        complete: function(xhr, status){}
});
```
```

对于经过了 Deferred 包装过的 $.ajax，其实我们更应该这样写：

```
```
$.ajax({
        url: 'test.php',
        data: {test: 123}
})
.done(function(data, status, xhr){})
.fail(function(xhr, status, error){})
.complete(function(xhr, status){})
```
```

Deferred 实现了将异步任务以及其回调函数的解耦，为 Ajax模块，队列模块，ready模块提供底层的基础。 异步队列模块主要为 $.when -> $.Deferred -> $.callbacks，依赖关系。 `$.Callbacks( flags )` 回调函数列表主要用于对于回调函数的添加，删除，触发，锁定以及禁用。jQuery 中的 Ajax 模块和 Deferred 模块功能强而不乱很大程度上也是归功于 Callbacks 模块的经典设计。 flags 参数：

*   once: 确保回调列表只会执行一次。
*   memory: 保存上一次的触发值，在回调列表添加新的函数时，使用保存的触发值进行触发。
*   unique: 确保每一个触发值，只会被回调列表的同一个函数触发一次。
*   stopOnFalse: 当一个回调返回false时 停止运行。

以下：

```
```
function fn1 (val) {
        console.log('fn1 : '+val);
}
function fn2 (val) {
        console.log('fn2 : '+val);
}
function fn3 (val) {
        console.log('fn3 : '+val);
}
```
```

`once：` ![](http://blog.chinaunix.net/attachment/201602/29/26672038_1456754259nLf8.png) `memory：` ![](http://blog.chinaunix.net/attachment/201602/29/26672038_1456754280EOuw.png) `unique：` ![](http://blog.chinaunix.net/attachment/201602/29/26672038_14567543041rKg.png) `stopOnFalse：` ![](http://blog.chinaunix.net/attachment/201602/29/26672038_1456754329tJXK.png) 将回调函数模块处理分割成 4 种状态这种思想，可以看到各个状态是互不排斥，并且可以随意搭配来生成一个回调函数列表，使其具有一种或者多种的状态值： 所依赖的底层功能函数的灵巧性，也造就了顶层代码功能的强大。其源码结构如下：

```
```
jQuery.Callbacks = function( options ) {
    var options = createOptions(options);
    var memory,
        // 是否已经 fire 过
        fired,
        // 当前是否还处于 firing 过程中
        firing,
        // fire 调用的起始下标
        firingStart,
        // 需要 fire 调用的队列长度
        firingLength,
        // 当前正在 firing 的回调在队列的索引
        firingIndex,
        // 回调队列
        list = [],
        // 如果不是 once 的，stack 实际上是一个队列，用来保存嵌套事件 fire 所需的上下文跟参数
        stack = !options.once && [],
        _fire = function( data ) {
        };

    var self = {
        add : function(){},
        remove : function(){},
        has : function(){},
        empty : function(){},
        fireWith : function(context, args){
                _fire([context, args]);
        };
        fire : function(args){
                this.fireWith(this, args);
        }
        /* 其他方法 */
    }
    return self;
};
```
```

其主要原理就是使用闭包特性，缓存各类状态值，在 add 方法中加入大量的逻辑判断实现。 再来简单说说 promise ，一个 promise 一般来说是处于三个互斥状态中的一个：

*   pending：进行中。
*   fulfilled：通过状态。
*   rejected：拒绝状态。

jQuery 的 Deferred 就是一个基于 Callbacks 的实现遵循 Common Promise/A 规范的一个功能。是的 jQuery 中许多功能方法得以实现异步的任务与回调函数的解耦。具体的，Deferred 有什么作用？ 比如说某一个情况，我需要在某个异步行为完成之后，执行特定的回调函数，那么基本会这么写

```
```
function asyn( callback ){
        setTimeout(function(){
                //....... // 执行的逻辑代码
                callback('123'); // 异步回调函数
        }, 100);
}

asyn( function(val){ alert(val) } );
```
```

就是说，我必须在异步函数调用的时候，就要写好，传入回调函数，导致了上面所执行的逻辑代码必须和异步回调函数放在一起，导致了一定的耦合性，当遇到回调中嵌回调的时候，就可能出现嵌套地狱的情况。

```
```
function asyn(){
        var dtd = $.Deferred();
        setTimeout(function(){
                //....... // 执行的逻辑代码
                dtd.resolve('123'); // 异步回调函数
        }, 100);
        return dtd;
}
var dtd = asyn();
dtd.done(function(val){ alert(val) });
```
```

其源码则是基于 Callbacks 的演化：

```
```
Deferred: function( func ) {
        var tuples = [
                        // action, add listener, listener list, final state
                        [ "resolve", "done", jQuery.Callbacks("once memory"), "resolved" ],
                        [ "reject", "fail", jQuery.Callbacks("once memory"), "rejected" ],
                        [ "notify", "progress", jQuery.Callbacks("memory") ]
                ],
                state = "pending",
                promise = {
                        state: function() {
                        },
                        always: function() {
                        },
                        then: function( /* fnDone, fnFail, fnProgress */ ) {
                        },
                        promise: function( obj ) {
                        }
                },
                deferred = {};

        promise.pipe = promise.then;
        ...
        return deferred;
}
```
```

Deferred 的 done 和 fail 传递回调函数分别对应在 resolve 和 reject 调用之后触发，或者在resolve 和 reject 调用之后，done fail 绑定回调函数的时候触发。当然 Deferred 还包括 then，promise，always等功能方法。 所以说，当无穷的嵌套地狱困扰回调函数的时候，不妨试试 promise 的解耦回调。 当然还有个不得不说的 $.when，当回调依赖回调形成嵌套回调的时候，可以使用 Deferred 很好的来解决，当回调依赖于多块异步逻辑代码的时候，经过 Deferred 封装的 when 就展现光彩了。

```
```
var d1 = $.Deferred();
var d2 = $.Deferred();

$.when( d1, d2 ).done(function ( v1, v2 ) {
        console.log( v1 ); // "Fish"
        console.log( v2 ); // "Pizza"
});
setTimeout(function(){
        dresolve( "Fish" );
}, 1000);

setTimeout(function(){
        dresolve( "dog" );
}, 3000);
```
```

很对情况就是像，当我们展现某块数据，而数据依赖多个数据接口的时候，非常有用。