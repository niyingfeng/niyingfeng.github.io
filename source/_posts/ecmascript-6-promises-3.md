---
title: ECMAScript 6 promises（下）：谈谈 API（二）
date: 2015-04-14
tags: 
- promise
---

原文地址： [http://www.2ality.com/2014/10/es6-promises-api.html](http://www.2ality.com/2014/10/es6-promises-api.html)
原文作者：Dr. Axel Rauschmayer

译者：倪颖峰

博文下半部分为纯干活内容，包括介绍的 ES6 中 Promise API，以及其简单的实现方式与思想，以及一些拓展内容。

## 9. 速查表：ES6的 promise API

这边简单的给出一下 ES6 promise 的API 简述，详细描述请看[[文档\]](https://people.mozilla.org/~jorendorff/es6-draft.html#sec-promise-objects)
	
### 9.1. 术语

promise 的 API 就是关于异步获取结果。一个 promise 对象是一个独立的对象，但会通过该对象传递结果。

**状态：**

- 一个promise 一般来说会处于以下三种互斥状态中的一种：
	- 结果未完成时，promise 状态为 pending。
	- 结果通过时，promise 状态为 fulfilled。
	- 当错误发生时，promise 状态变为 rejected。
- 一个promise 一旦被设置则为“运行完成”（包括 fulfilled 或者 rejected）。
- 一个peomise 一旦被设置之后便不能在改变。

**状态变化反应：**

Promise reastions 就是你是用 promise 方法 then 来注册的回调函数，用来监听状态 fulfillment 和 rejected。

一个 then式即为一个含有 promise 风格方法 then 的对象。即使该方法仅仅只是监听状态的，依然为 then 式。

**状态改变：**

有两种改变 promise 状态的方式。一旦你调用了其中的一个，那么进一步调用将会无效。

- 拒绝一个 promise 表示这个promise 被设为 rejected。
- 通过一个 promise 有两种情况，取决于以什么值来解决的：
	- 使用一个普通值（非 then式）来解决 promise。
	- 解决 promise P 以一个 then式的 T，意味着P 不再可以设置 resolved，而将会随着 T的状态走，即其 fulfillment 和 rejection 值。P 的回调会在T 被设置状态后合适的时候触发（或者如果T 已经被设置状态了，那么就会立即触发）。

### 9.2. 构造函数

promise 的构造函数为以下形式：

```
var p = new Promise(executor(resolve, reject));
```

上面创建了一个行为由回调函数 exector 决定的 promise。使用参数来处理解决或者拒绝 p：

- resolve(x)，以 x 来解决 p：
	- 如果 x 是 then式的，那么它的结果会转到 p（包括通过 then() 注册的触发反馈）。
	- 否者，p 就以 x 处理 fulfilled 状态。
- reject(x)，以 e 值来拒绝 p（通常为一个 Error 的子类）。

### 9.3. 静态方法

所有的 Promise 的静态方法都支持实例化：通过接收器来创建一个实例（像：new this），并且通过它来访问这些静态方法（this.resolve 与 Promise.resolve）。

**创建 promises：** 下面两种方式来创建接收器的实例。

- Promise.resolve(x):
	- 如果 x 是 then式的，它将转化为一个 promise（接收器的实例）。
	- 如果 x 是一个 promise，返回将没有变化。
	- 否者将会返回一个接收器的实例并且以 x 来处理 fulfilled 状态。
- Promise.reject(reason)：创建一个新的promise，并且以 reason 值来处理拒绝状态。

**组合 promises：** 直观来说，静态方法 Promise.all() 和 Promise.race() 组合迭代的 promises 变为一个单一的 promise。即：

- 它们采用迭代。迭代的元素通过 this.resolve() 来转化为 promises。
- 它们返回一个新的 promise。这个 promise 返回一个新的接收器的实例。

**方式有：**

- Promise.all(iterable)：如此返回一个 promise
	- 如果迭代的元素均为 fulfilled 那么将其设置为 fulfilled。成功值：各个成功值生成的数组。            
	- 当元素中有任何一个失败时便设置其为 rejected。拒绝值：第一个解决状态的值。
- Promise.race(iterable)：迭代的第一个被设置完毕的元素被用来解决返回的 promise。

### 9.4. 继承原型方法

Promise.prototype.then(onFulfilled, onRejected);

- 回调函数 onFulfilled 和 onRejected 被车称为 reactions。
- 如果该 promise 已经被设为 fulfilled，或者一旦变成 fulfilled 状态，那么回调函数 onFulfilled 就会被立即执行。相同，被触发之后 onRejected 也一样。
- then() 返回一个新的 promise Q（通过接收器的构造函数创建）：
	- 任意 reactions 返回一个结果，Q 便使用其来通过。
	- 任意 reactions 抛出一个错误，Q 便使用其来拒绝。
- 触发 reactions：
	- 如果 onFulfilled 被触发了，那么一个通过状态的接收器就会被转发到 then() 的结果中。
	- 如果 onRejected 被触发了，那么一个拒绝状态的接收器就会被转发到 then() 的结果中。

触发 reactions 的默认值可以以以下方式执行：

```
function defaultOnFulfilled(x){
	return x;
}
function defaultOnRejected(e){
	throw e;
}
```

**Promise.prototype.catch(onRejected)：** 与 then(null, onRejected) 一样。

## 10. promises 的优势与劣势

### 10.1. 优势

**统一的异步 APIs**
promises 的一个重要的优点就是被越来越多的浏览器的异步 API 使用，统一了当前多样化和不兼容的各种模式和约定。看一下即将出现的两种基于 promise 的API。

基于 promise 的 ferch API 替代 XMLHttpRequest：

```
fetch(url)
.then(request => request.text())
.then(str => ...)
```

fetch() 会对实际的请求返回一个 promise，text() 会对内容作为字符串返回一个 promise。

ECMAScript 6 API 编程式导入模块也是基于 promise：

```
System.import('some_modle.js')
.then(some_module => {
	...
})
```

**promises VS events**

对比 events，promises 更适合做一次性结果的监听。不管你是在得到结果之前或者之后注册都不会影响你监听到结果。这是 promises 更本上的优势。另一方面，你不能使用它来处理一些反复性的事件。链式是         promise 的另一个优势，但是每次只能添加一个。

**promises VS callbacks**

对比回调函数，promises 具有更简洁的函数（方法）参数。在 callbacks 中，参数包含输入输出：

```
fs.readFile(name, opts?, function(err, data))
```

而 promise 中，所有的参数均只是输入：

```
readFilePromisified(name, opts?)
.then(dataHandler, errorHandler)
```

此外，promises 的优势还包括更好的错误处理机制（异常的集成）和组合更任意更容易（因为你可以使用一些同步的工具，比如 Array.prototype.map()）。

### 10.2. 劣势

Promises 对于单一的异步结果能处理的很好。但是它不擅长于：

- 反复性事件：如果你对此感兴趣，可以看一下 reactive programming（http://reactive-extensions.github.io/RxJS/），关于链式处理普通的事件监听的巧妙方式。
- 数据流：支持的标准正在完善中。

ECMAScript 6 的 promises 缺少一下的两个基本功能点：

- 你无法取消。
- 你无法确定他们要在多久之后发生（例如在客户端用户界面展现进度条）。

Q promises 库支持后者，并且也将计划添加到 Promises/A+。

## 11. promises 与 生成器

由于一个实用功能 Q.spawn()，你可以通过生成器来实现在浅协程里使用基于 promises 的函数。这是一份重要的优点使得代码看起来类似于同步，并且可以使用一些同步机制，比如说 try-catch：

```
Q.spawn(function* () {
	try {
			let [foo, bar] = yield Promise.all([ // (A)
					httpGet('foo.json'),
					httpGet('bar.json')
			]);
			render(foo);
			render(bar);
	} catch (e){
			console.log('Read failed: '+ e );
	}
});
```

Q.spawn() 的参数是生成器函数。如果 yield 标识符是有效的，那么将发生下面的事情：

- 函数执行将被暂停。
- 操作符 yield 被函数返回（返回，描述的不确切，暂时忽略）。
- 随后函数会被恢复成一个值或者异常。前者情况继续执行之前暂停的那个点，yield 返回一个值。后者情况表达式被抛入函数中，类似 yield 操作符从内部被扔入。

从而，很清楚的明白 Q.spawn() 所做的事情：当生成器函数产生一个 promise，spawn 便注册 reaction，等待处理。如果 promise 通过状态，生成器便会恢复一个结果。如果 promise 为拒绝，一个异常便会抛入注册器中。

这是一个通过新的语法结构-异步函数。来添加支持 JS spawn 的提案（https://github.com/lukehoban/ecmascript-asyncawait）。之前例子作为异步函数应该如下。底层，并没有较多不同 - 异步函数基于生成器。

```
asyc function(){
	try {
			let [foo, bar] = await Promise.all([
					httpGet('foo.json'),
					httpGet('bar.json')
			]);
			render(foo);
			render(bar);
	} catch (e){
			console.log('Read failed: '+ e );
	}
}
```

## 12. 调试 promises

主要的挑战是调试异步代码中包含异步函数和方法的调用。异步调用源自于一个任务执行了一个新的任务。如果该新任务中有异常，堆栈跟踪只会覆盖该任务，不会包含之前任务的信息。所以在异步编程中你只能获得很少的调试信息。

Google 的 chrome 最近开始可以调试异步代码。并不完全支持 promises，但是处理一般的异步编程做得非常不错。比如下面的实例，first 异步调用 返回调用 third 的 second。

```
function first(){
	setTimeout(function () { second('a') }, 0); //(A)
}
function second(){
	setTimeout(function () { third('b') }, 0); //(B)
} 
function third(){
	debugger;
}
first();
```

正如截图中所示，调试器展现一个包含三个函数的跟踪堆栈。它包含了 line A 和 line B 处的异步函数。

![](http://image.freefe.cc/20170125162922.png)

## 13. promises 内部窥秘

本段，我们将从不同的角度来看一下 promises：不再学习如何使用 promise API，我们将了解一下它的简单实现。这个视角将帮助我们实现一个 promises。

promises 的实现被称为 DemoPromise 和 在 GitHub 上可用的实例（https://github.com/rauschma/demo_promise）。为了更容易理解，将不会完全匹配 API。但是它将足够使你了解在实现 promises 过程会中面对的各种难题。

DemoPromises 是一个含有三个原型方法的构造函数：

- DemoPromises.prototype.resolve(value)
- DemoPromises.prototype.reject(reason)
- DemoPromises.prototype.then(onFulfilled, onRejected)

在此，resolve 和 reject 为方法（而不是传递给传递给构造函数作为参数的回调函数）。

### 13.1 一个独立的 promise

首先我们需要实现一个最少功能的独立的 promise ：

- 你可以创建一个 promise。
- 你可以通过或者拒绝一个 promise 并且只能处理它一次。
- 你可以通过 then() 来注册 reactions（回调函数）。该方法不支持链式（该处应该是支持链式），就是说，其不返回任何东西。它需要被立即执行不管该 promise 是否已经被处理。

以下是使用第一步所实现的：

```
var dp = new DemoPromise();
dp.resolve('abc');
dp.then(function(value){
	console.log(value); // abc
});
```

下面图标说明了我们第一个 DemoPromise 是如何实现的：

![](http://image.freefe.cc/20170125162939.png)

让我们首先研究下 then()。它必须处理两个情况：

- 如果 promise 还在执行中，那么将在 promise 被处理完毕之后调用 onFulfilled 或者 onRejected 的时候使用。
- 如果 promise 已经被通过或者拒绝，onFulfilled 或者 onRejected 将被立即调用。

```
DemoPromise.prototype.then = function (onFulfilled, onRejected) {
	var self = this;
	var fulfilledTask = function () {
			onFulfilled(self.promiseResult);
	};
	var rejectedTask = function () {
			onRejected(self.promiseResult);
	};
	switch (this.promiseState) {
			case 'pending':
					this.fulfillReactions.push(fulfilledTask);
					this.rejectReactions.push(rejectedTask);
					break;
			case 'fulfilled':
					addToTaskQueue(fulfilledTask);
					break;
			case 'rejected':
					addToTaskQueue(rejectedTask);
					break;
	}
};
function addToTaskQueue(task) {
	setTimeout(task, 0);
}
```

**resolve() 工作原理如下：**如果 promise 已经被处理完毕，将不做任何事情（确保一个 promise 只被处理一次）。否则，promise 的状态转变为 fulfilled 并且结果缓存到 this.promiseResult。所有入队到通过状态 reactions 的将被立即执行。

```
Promise.prototype.resolve = function (value) {
	// 为执行回调函数方法 如果不为状态 pending 为在 then 时刻已经处理执行过，立即跳出
	if (this.promiseState !== 'pending') return;
	this.promiseState = 'fulfilled';
	this.promiseResult = value;
	this._clearAndEnqueueReactions(this.fulfillReactions);
	return this; // 链式
};
Promise.prototype._clearAndEnqueueReactions = function (reactions) {
	this.fulfillReactions = undefined;
	this.rejectReactions = undefined;
	reactions.map(addToTaskQueue);
};
```

reject() 与 resolve() 类似。

### 13.2 链式（注意，这部分已经为下一步扁平化做好了基础）

接下来，我们将实现链式：

- then() 返回的 promise 实际上是 onFulfilled 或者 onRejected 被执行后所返回的。
- 如果 onFulfilled 或者 onRejectecd 缺失，那么不管它们所传递的是什么， promise 通过 then() 返回出来。

![](http://image.freefe.cc/20170125162959.png)

显然，只需要调整下 then()：

```
DemoPromise.prototype.then = function (onFulfilled, onRejected) {
	var returnValue = new DemoPromise(); // (A)
	var self = this;

	var fulfilledTask;
	if (typeof onFulfilled === 'function') {
			fulfilledTask = function () {
					var r = onFulfilled(self.promiseResult);
					returnValue.resolve(r); // (B)
			};
	} else {
			fulfilledTask = function () {
					returnValue.resolve(self.promiseResult); // (C)
			};
	}

	var rejectedTask;
	if (typeof onRejected === 'function') {
			rejectedTask = function () {
					var r = onRejected(self.promiseResult);
					returnValue.resolve(r); // (D)
			};
	} else {
			rejectedTask = function () {
					// Important: we must reject here!
					// Normally, result of `onRejected` is used to resolve
					returnValue.reject(self.promiseResult); // (E)
			};
	}
	...
	return returnValue; // (F)
};
```

then() 创建并返回一个新的 promises（在 A 处和 F 处）。此外，fulfilledTask 和 rejectedTask 以不同方式设置：在处理结束时。

- onFulfilled 的结果被用在通过 returnValue（在 B 处）。如果 onFulfilled 为缺失的，我们使用通过的值来处理 returnValue（在 C 处）。
- onRejected 的结果被用于通过（并非拒绝） returnValue（在 D 处）。如果，onRejected 缺失，我们使用拒绝的值来拒绝 returnValue（在 E 处）。

### 13.3 扁平化

扁平化思想主要是使链式方式更为便捷：一般，将一个 reaction 返回的值传递到下一个 then()。如果我们返回一个 promise，不包裹的形式是最好的，像下面的例子：

```
asyncFunc1()
.then(function (value1){
	return asyncFunc2(); // A
})
.then(function (value2){
	// value2 为 asyncFunc2() promise 的通过状态。
	console.log(value2);
});
```

我们在 A 处返回一个promise，就没有必要在当前方法中嵌套 then() 的调用，我们可以对方法的结果进行 then() 的调用。那么，不嵌套 then()，就保持扁平化。

我们通过使 resolve() 方法扁平化来实现整体扁平化：
- 以一个 promise Q 通过一个 promise P 意味着 Q 的处理结果要在 P 的 reactions 之前。
- 在 Q 中，P 应该被锁定：它不能被通过（或者拒绝）。并且它的状态和结果和 Q 的保持同步。

如果我们使用 then式 （而非一个 promise）能以更通用的形式实现扁平化。

![](http://image.freefe.cc/20170125163015.png)

实现 锁住，我们需要使用一个布尔值标签 this.already 。一旦值为 true，this 将被锁住，不能在被通过，记录如果 this 仍在进行中，因为它的状态是和锁住的 promise 是一致的。

```
DemoPromise.prototype.resolve = function (value) {
	if (this.alreadyResolved) return;
	this.alreadyResolved = true;
	this._doResolve(value);
	return this; // enable chaining
};
```

真实的解决代码在私有方法 _doResolve() 中：

```
DemoPromise.prototype._doResolve = function (value) {
	var self = this;
	// Is `value` a thenable?
	if (value !== null && typeof value === 'object'
			&& 'then' in value) {
			addToTaskQueue(function () { // (A)
					value.then(
							function onFulfilled(value) {
									self._doResolve(value);
							},
							function onRejected(reason) {
									self._doReject(reason);
							});
			});
	} else {
			this.promiseState = 'fulfilled';
			this.promiseResult = value;
			this._clearAndEnqueueReactions(this.fulfillReactions);
	}
};
```

扁平化处理在 A 处进行：如果 value 为通过状态，那么当前 self 为通过状态，如果 value 为拒绝的，我们就希望 self 为拒绝状态。前面所述通过私有方法 _doResolve 和 _doReject 来实现，通过 alreadyResolved 来绕开阻挡。

### 13.4. 更详细的 Promise 状态

由于链式形式，promises 的状态变得更为复杂。

![](http://image.freefe.cc/20170125163029.png)

如果你只是使用 promises，你只需要以简单的视角来看待，忽略锁定就好。最主要的状态相关概念是“settledness”： 一个 promise 在被通过或者拒绝的时候被设置完毕。在一个 promise 被设置之后，将不再变化（状态为通过状态或者拒绝状态）。

如果你希望先处理 promsie，然后再将其通过，这便会很难理解：

- 直观的说，‘resolved’（已处理）意味着不能再次被（直接）的处理。一个 promise 在只在即未被设置过也不在锁定的状态下被解决。引用下规范：一个未被处理的 promise 一般处在 pending 状态。一个已经处理的 promise 可能为 等待，通过或者拒绝状态。
- 处理中不一定会使其被设置：你可以通过一个 promise 以另一个状态为 pending的。
- 处理中现在包含拒绝：你可以拒绝一个 promise 通过一个拒绝的 promise 处理。

### 13.5. 异常

在我们不久的将来，我们将会在 user code 中以 rejections 形式处理异常。目前来说，user code 只代表 then 中的两个回调函数参数。

![](http://image.freefe.cc/20170125163044.png)

下面代码片段展现了我们在 onFulfilled 内部处理异常转到 拒绝状态 - 通过 A 处调用时的 try-catch 包裹。

```
var fulfilledTask;
if (typeof onFulfilled === 'function') {
	fulfilledTask = function () {
			try {
					var r = onFulfilled(self.promiseResult); // (A)
					returnValue.resolve(r);
			} catch (e) {
					returnValue.reject(e);
			}
	};
} else {
	fulfilledTask = function () {
			returnValue.resolve(self.promiseResult);
	};
}
```

### 13.6. 揭示构造函数模式

如果我们希望将 DemoPromise 转变为切实可用的 promise，我们还需要实现揭示构造函数模式：ES6 promises 不是通过方法来进行通过或者拒绝，而是通过监听在 executor 上的函数实现的，构造函数的参数。

![](http://image.freefe.cc/20170125163059.png)

如果执行函数抛出一个异常，该 promise 一定被拒绝。


## 14. 两个常用的 promise 附加方法

本段介绍一下两个在 ES6 中添加到 promsie 的方法。promise 库的大部分都支持他们。

### 14.1. done()

当你将数个 promise 方法链式调用时，你可能会无心之中忽略错误。看实例：

```
function doSomeThing(){
	asyncFunc()
	.then(f1)
	.catch(r1)
	.then(f2); // A
}
```

如果在 A 处的 then() 生成拒绝状态，那么将不会被处理。 promise 库 Q 提供了一个方法 done()，放置在链式最后一个方法调用的后面。其或者替换最后一个 then()（也有一个到两个参数）：

```
function doSomeThing(){
	asyncFunc()
	.then(f1)
	.catch(r1)
	.done(f2);
}
```

又或者仅仅是插到最后一个 then() 之后（无参数）：

```
function doSomeThing(){
	asyncFunc()
	.then(f1)
	.catch(r1)
	.then(f2)
	.done();
}
```

引用 Q 的文档：

> done 与 then 使用的黄金规则：既不返回任何一个 promise，或者结束链式调用，那么使用 done 来终止。使用 catch 来终止不是很好，因为 catch 的可能是其自己内部的错误。

这也是在 ES6 中实现  done 的方式：

```
Promise.prototype.done = function(onFulfilled, onRejected){
	this.then(onFulfilled, onRejected)
	.catch(function(reason){
			setTimeout( ()=>{ throw reason }, 0 );
	});
};
```

虽然 done 功能极为有用，但是没有添加到 ES6 中，因为在将来这种检测可以被调试器自动调起（一个 ES 讨论版本中）。

### 14.2. finally()

有时你希望某个行为不管有没有错误抛出都立即执行。例如在一系列行为后的处理。这便是 promise 方法 finally() 所做的，极为类似与错误处理机制中的 finally 。其回调方法不接受参数，但是会有 通过状态或者拒绝状态的通知。

```
createPeaource(...)
.then(function(value1){ ... })
.then(function(value2){ ... })
.finally(function(){ ... });
```

这是 Domenic Denicola 计划实现的 finally()：

```
Promise.prototype.finally = function (callback) {
	let p = this.constructor;
	// 不在此调用回调函数,
	// 希望使用 then 来处理
	return this.then(
			// Callback fulfills: 传递参数结果
			// Callback rejects: 传递拒绝状态
			value => p.resolve(callback()).then(() => value),
			reason => p.resolve(callback()).then(() => { throw reason })
	);
};
```

回调函数决定了 接收器（this）如何设置处理：
- 如果回调函数抛出一个错误或者返回一个拒绝状态的 promise，那么变为 拒绝状态的值。
- 否则，接收器的设置变为的 finally 返回的 promise 的设置。使用 finally() 的链式方法。

**例一：使用 finally() 来隐藏 spinner：**

```
showSpinner();
fetchGalleryData()
.then(data => updateGallery(data) )
.catch(showNoDataError)
.finnally(hideSpinner);
```

**例二：使用 finally 来清除测试**

```
var HTTP = require('q-io/http');
var server = HTTP.Server(app);
return server.listen(0)
.then(function(){ ... })
finally(server.stop);
```

## 15. ES6 兼容的 promise 库

下面有一些 promise 的库，有一些 ES6 的API，意味着你在将来也可以用原生 ES6 代码替换。

- RSVP.js 来自 Stefen Penner，为 ES6 promise API 的超集。
- Native Promise Only 来自 Kyle Simpson 是原生 ES6 promise 的 polyfill，以严格模式来定义，尽可能接近而不扩展。
- Lie 来自 Calvin Metcalf 小巧，完善的 promise 库，遵循 Promises/A+ 规范。
- Q.promise 来自 Kowal 实现 ES6 API。
- 最近的，来自 Paul Millr 包含 promise 的 ES6 Shim。

## 16. 传统的异步代码的接口

放你使用一个 promsie 库时，有时是基于不支持 promsie 的异步代码。这段讲述一下 Node.js 风格的异步函数与 jQuery deferreds。

### 16.1 Node.js 的接口
promise 库 Q 有几个工具方法来转换函数使其使用 Node.js 风格(err, result)的回调函数然后返回一个promise（还有一些做一些相反的事情，转换基于 promise 的函数来为一些接受回调函数的函数）。例如：

```
var readFile = Q.denideify( FS.readFile );
readFile('foo,txt', 'utf-8')
.then(function(text){
	....
});
```

denodify 为一个符合 ES6 promise API ，提供 nodification 函数式化功能。

### 16.2. jQuery 的接口

jQuery 的 deferred 类似于 promise，但是也有几个兼容性方面的不同。方法 then() 都类似与 ES6 promises（主要不同之处是：不会在 reactions 中监控错误抛出）。我们可以通过 Promise.resolve() 将 一个 jQuery deferred 为 ES6 的 promise：

```
Promise.resolve(
	jQuery.ajax({...})
).then(funciton(data){
	console.log(data);
}).catch(function(reason){
	console.error(reason)
});
```

## 17. 延伸阅读

1. Promises/A+（http://promisesaplus.com/）：Brian Cavalier 与 Domenic Denicola 编辑（JS promise 事实标准）
2. Javascript Promises：再次强势归来（http://www.html5rocks.com/en/tutorials/es6/promises/）：来自 Jake Archibad（挺不错的 promise 简要介绍）
3. Promsie 反模式（http://taoofcode.net/promise-anti-patterns/）：来自 Tao（要点与技术）
4. Promise 模式（https://www.promisejs.org/patterns/）来自 Forbes Lindeasy
5. 揭示构造函数模式（http://domenic.me/2014/02/13/the-revealing-constructor-pattern/）：来自 Domenic Denicola（为 Promise 构造函数使用的模式）
6. Chrome 开发者工具调试异步 JS 代码（http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/）：来自 Pearl Chen
7. ES6 中的迭代器和生成器（http://www.2ality.com/2013/06/iterators-generators.html）
