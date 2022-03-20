---
title: ECMAScript 6 promises（下）：谈谈 API（一）
date: 2015-03-17
tags: 
- promise
---
原文地址： [http://www.2ality.com/2014/10/es6-promises-api.html](http://www.2ality.com/2014/10/es6-promises-api.html)
原文作者：Dr. Axel Rauschmayer

译者：倪颖峰

（文章第二部分实在是太长，所以在此分成两部分翻译）
本文是通过普通的 promises 和 ES6的 promise API 来介绍异步编程。这是两篇系列文章的第二部分 - 第一部分介绍了一下异步编程的基础（你需要充分理解一下以便明白这篇文章）。

鉴于 ES6 的 promise API 比较容易从 ES5 标准模拟生成过来，所以这边主要使用函数表达式，而不使用更为简洁的 ES6 中的箭头函数。

## 1. Promises

Promises 是一种来解决特定异步编程的模式：函数（或者方法）异步返回其结果。实现一个对返回结果的具有占位符意义的对象 promise。函数的调用者注册回调函数，一旦结果运算完毕就立即通知 promise。函数会由 promise 来传递结果。

JavaScript 的 promises 事实标准称为 Promises/A+。ES6 的 promise API 便遵循这个标准。

## 2. 第一个实例

看一下第一个实例，来了解下 promises 是如何运行的。

NodeJS 风格的回调函数，异步读取文件如下所示：

```
fs.readFile('config.json', function (error, text) {
	if (error) {
			console.error('Error while reading config file');
	} else {
			try {
					var obj = JSON.parse(text);
					console.log(JSON.stringify(obj, null, 4));
			} catch (e) {
					console.error('Invalid JSON in file');
			}
	}
});
```

使用 promises，相同功能的实现可以是这样：

```
readFilePromisified('config.json')
.then(function (text) { // (A)
	var obj = JSON.parse(text);
	console.log(JSON.stringify(obj, null, 4));
})
.catch(function (reason) { // (B)
	// File read error or JSON SyntaxError
	console.error('An error occurred', reason);
});
```

这边依旧是有回调函数，但是这里通过方法来提供的，避免了放在结果中（then() 和 catch()）。在 B 处的报错的回调函数有两方面的优势：第一，这是一种单一风格的监听错误。 第二，你可以同时处理  readFilePromisified() 的 和 A 处回调函数的错误。


## 3. promises 的创建和使用

从生成形式和消耗形式两方面来看一下 promises 是如何操作的。

### 3.1. 生成一个 promise

作为一个生成形式，你创建一个 promise 然后由它传递结果：

```
var promise = new Promise( function( resolve, reject ){
	...
	if( ... ){
			resolve( value );
	} else {
			reject( reason );
	}
} );
```

一个 promise 一般具有一个到三个（互斥）的状态：

> Pending：还没有得到结果，进行中
> Fulfilled：成功得到结果，通过
> Rejected：在计算过程中发生一个错误，拒绝

一个 promise 被设置过后（ 代表运算已经完成 ）那么它要么是 fulfilled 要么是 rejected。每一个 promise 只能被处理一次，然后保持处理后的状态。之后在处理它将不会触发任何效果。

![](http://image.freefe.cc/20170125155753.png)

new Promise() 的参数（ 在 A 处开始的 ）称为 executor（执行者）:

1. 如果运算成功，执行器会将结果传递给 resolve()。那属于 promise 通过（后面将会解释，如果你处理一个 promise 可能会不同）。
2. 如果错误发生了，执行器就会通过 reject() 通知 promise 消耗形式。就会解决该 promise。

### 3.2. 消耗一个promise

作为 promise 的消耗形式，你会将一个完成态或者通过态通知给 reactions - 通过 then 方法注册的回调函数。

```
promise.then( function( value ){
	/* fulfillment */
}, function( reason ){
	/* rejection */
} );
```

正是由于一个 promise 仅能被处理一次，之后再也不能变化使得 promises 对于异步函数来说异常有用（一次性使用结果）。此外，还没有任何其他的条件，因为在 promise 被处理时间点的前后调用 then() 是一样的：

1. 前者的情况，合适的反馈调用会在 promise 被处理时立即调用。
2. 后者情况，promise 的结果（通过的值或者拒绝的值）会被缓存起来，在合适的请跨过下立即处理反馈（排列一个任务）

### 3.3. 只处理通过态或者拒绝态

如果你只关心通过状态，你可以忽略 then() 的第二个参数：

```
promise.then(function( value ){
	/* fulfillment */
});
```

如果你只需要通过状态，你可以忽略第一个参数。用 catch() 方法也可以更紧凑的来实现。

```
promise.then( null, function( reason ){
		 /* rejection */
} );
// 等价于
promise.catch( function( reason ){ 
	/* rejection */
} );
```

这里推荐只是用 then() 处理成功状态，使用 catch() 处理错误，因为这样可以更加优雅的标记回调函数并且你可以同时对多个 promises 进行通过态处理（稍后解释）。

## 4. 实例

让我们在一些例子中使用一下基本构成形式。

### 4.1. 例：promise 化 XMLHttpRequest

下面是一个基于 XMLHttpRequest API 事件，通过 promise 编写的 HTTP GET函数。

```
function httpGet( url ){
	return new Promise( function( resolve, reject ){
			var request = new XMLHttpRequest();
			request.onreadystatechange = function(){
					if( this.status === 200 ){
							// success
							resolve( this.response );
					}else { 
							reject( new Error( this.statusText ) );
					}
			}
			request.onerror = function(){
					reject( new Error( 'XMLHttpRequest Error: ' + this.statusText ) );
			}
			request.open( 'GET', url );
			request.send();
	} );
}
```

下面是如何使用 httpGet()：

```
httpGet("http://example.com/file.txt")
	.then(function(){
			console.log('contents: '+ value);
	}, function( reason ){
			console.log('something error', reason);
	});
```

### 4.2. 例子：延迟执行

使用 setTimeout() 来实现基于 promise 的 delay()（类似于Q.delay()）。

```
function delay( ms ){
	return new Promise(function( resolve, reject ){
			sertTimeout( resolve, ms ); // (A)
	});
}
// 使用 delay()
delay( 5000 ).then(function(){ // (B)
	console.log('5s have passed');
});
```

注意 A 处我们调用 resolve 没有传递参数，相当于我们这么调用 resolve( undefined )。在 B 处我们不需要通过的返回值，就可以简单的忽略它。仅仅通知就已经OK了。

### 4.3. 暂停 promise

```
function timeout( ms, promise ){
	return new Promise(function( resolve, reject ){
			promise.then( resolve );
			setTimeout(function(){
					reject(new Error('Timeout after ' + ms + ' ms')); // (A)
			}, ms)
	});
}
```

注意在 A 处的超时失败并不会阻止这个请求，但是可以确保 promise 的成功状态结果。

如下方式使用 timeout()：

```
timeout( 5000, httpGet("http://example.com/file.txt") )
	.then(function( value ){
			console.log('contents: ' + value);
	})
	.catch(function( reason ){
			console.log('error or timeout: ' , reason);
	});
```

## 5. 链式调用 then()

方法调用结果 P.then( onFulfilled, onRejected ) 是一个新的 promise Q。这意味着在 Q 中，你可以通过调用 then() 来保持对基于 promise 流的控制：

1. Q 在 onFulfilled 或者 onRejected 返回结果的时候，即为通过。
2. Q 在 onFulfilled 或者 onRejected 抛出错误的时候，即为拒绝。

### 5.1.  一般值来通过

如果你通过使用 then() 返回一个一般值来处理一个 Q，那你就可以通过下一个 then 来取到这个值：

```
asyncFunc().then( function(){
	return 123;
} ).then( function( value ){
	console.log( value ); // 123
} )
```

### 5.2. 通过 then式 来通过

你也可以通过 then() 来返回一个 then式的 R对象 来将 promise Q 通过。then式表示有 promise 风格方法 then() 的任何对象。即， promises 便是 then式的。处理 R （例如将其以 onFulfilled 来返回）意味着他是被插入到 Q 之后的：R的结果将被作为 Q onFulfilled 或者 onRejected 的回调函数。也就是说 Q 转变为 R。

![](http://image.freefe.cc/20170125155818.png)

这个形式主要是用来扁平化嵌套式调用 then()，比如下面的例子：

```
asyncFunc1()
.then(function(value1){
	asyncFunc2()
	.then(function(value2){
			...
	});
});
```

那么扁平化形式可以变为这样：

```
asyncFunc1()
.then(function(value1){
	return asyncFunc2();
})
.then(function(value2){
	...
});
```

## 6. 错误处理

如之前提到的，不管你在错误中返回什么都讲成为一个 fulfillment 的值（或者 rejection 值）。这使得你可以定义失败执行的默认值：

```
retrieveFileName()
.catch(function(){
	return 'Untitled.txt';
}).then(function(fileName){
	...
});
```

### 6.1. 捕获异常

执行过程中的异常将会传递到下一个错误处理中：

```
new Promise(function(resolve, reject){
	throw new Error();
})
.catch(function(err){
	// Handle error here
});
```

作为异常，会将其作为then的一个参数抛出：

```
asyncFunc()
.then(funcrion(value){
	throw new Error();
})
.catch(function(reason){
	// Handle error here
})；
```

### 6.2. 链式的错误处理

将会有一个或者多个的 then() 调用但并没有提供一个错误处理。那么直到出现错误处理该错误才会被传递出来：

```
asyncFunc1()
.then(asyncFunc2)
.then(asyncFunc3)
.catch(function(reason{
	// something went wrong above
});
```

## 7. 组合

本段会描述你如何将先现有的 promises 来组合创建新的 promise。我们已经使用过一种组合 promise 的方式了：通过 then() 连续的链式调用。Promise.all() 和 Promise.race() 提供了另一些组合的形式。

### 7.1. 通过 Promise.all() 实现 map()

庆幸的是，基于 promise 返回结果，很多同步的工具仍然可以使用。比如你可以使用数组的方法 map()：

```
var fileUrls = [
	'http://example.com/file1.txt',
	'http://example.com/file2.txt'
];
var promisedTexts = fileUrls.map(httpGet);
```

promisedTexts 为一个 promises 对象的数组。Promise.all() 可以处理一个 promises 对象的数组（then式 和 其他值可以通过 Promise.resolve() 来转换为 promises）一旦所有的项状态都为 fulfilled，就会处理一个他们值得数组：

```
Promise.all(promisedTexts)
.then(function(texts){
	texts.forEach(function(text){
			console.log(text);
	});
})
.catch(function(reason){
	// 接受 promises 中第一个 rejection 状态
});
```

### 7.2. 通过 Promise.race() 实现延时

Promise.race() 使一个 promises 对象数组（then式 和 其他值可以通过 Promise.resolve() 来转换为 promises）返回一个 promise 对象 P。输入第一个的 promises 会将其结果通过参数传递给输出的 promise。

举个例子，来使用 Promise.race() 来实现一个 timeout：

```
Promise.race([
	httpGet('http://example.com/file.txt'),
	delay(5000).then(function(){
			throw new Error('Time out');
	})
])
.then(function(text){ ... })
.catch(function(reason){ ... });
```

## 8. Promises 基本都是异步的

一个 promise 库，不管结果是同步（正常方式）还是异步（当前延续的代码块完成之后）进行的传递都做好控制。然而，Promises/A+ 规范约定后一种方式应总是可用的。它以以下的条件来将状态传给 then()：

> 当执行上下文栈中只包含平台代码时才执行 onFulfilled 或者 onRejected。

这意味着你可以依赖运行到完成的语态（第一部分中提到的），使得链式的 promises 不会使其他任务在闲置时处于等待状态。
