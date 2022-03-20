---
title: node之Buffer 介绍
date: 2014-03-05
tags: 
- node
- buffer
---
好吧，本以为node中的Buffer只是nodeJS中挺小的一块，仔细的翻阅了一些资料之后才发现，冰川总是将其巨大的屁股藏在海平面以下的，这次也是主要简单的讲一下关于Buffer比较浅的一些东西（针对Node初学者啦~）

## Buffer 是什么？

对于JavaScript来说，对Unicode编码的数据很容易处理的，但是对于二进制数据就没什么处理方法了。

但是在一些TCP数据流或者在操作一些文件数据流的时候，字节流的处理方法还是必须的。

nodeJS中便出了处理的策略方法~提供了与String类似对等的全局构造函数Buffer（与其说与String对等，还不如说与Array类似但是有些不同方面还是需要注意），全局那么自然就不要每次的require了。

> Buffer则node中储存二进制数据的中介者。

**创建Buffer实例的方式：**

```
new Buffer( size );
```

和数组类似，给定所需要创建Buffer对象的大小。

![](http://blog.chinaunix.net/attachment/201403/25/26672038_13957579146WFB.png)

```
new Buffer( array );
```

给定二进制数据的一个数组形式排列

![](http://blog.chinaunix.net/attachment/201403/25/26672038_1395758011Knm0.png)

```
new Buffer( str, [ encoding ] );
```

给与某一特定数据类型数据，以及其编码类型（默认均为utf8）

![](http://blog.chinaunix.net/attachment/201403/25/26672038_13957580902cvk.png)


**以及还有一些操作Buffer实例的方法：**

一个Buffer的形式非常类似于一个整数数组，与字符串还是有一定的区别的。

JS程序员都比较熟悉，对于字符串来说可以视为可读的，每次的修改，复制都会得到一个新的字符串，不会影响之前的字符串，就如有一个字符串a，将 var b = a;  a 和 b 是各会拥有独立的一份字符串数据，相互独立，与对象类似于指针的形式不同（按值传递和按址传递）。

不过对于字符串的兄弟Buffer来说，作为对象的Buffer含有的Array的血更多一点。

![](http://blog.chinaunix.net/attachment/201403/25/26672038_1395758215FBRf.png)

但是对于Buffer的一些方法进行使用的时候，还是需要注意其和数组一些不一致的地方

 ![](http://blog.chinaunix.net/attachment/201403/25/26672038_139575828735ZY.png)
 
在将创建的Buffer截取一段之后，对返回的Buffer段进行修改，还是会影响原Buffer，两者仍是关联的。这类似于一个储存一个个对象的数组，Buffer这个类数组中每一个元素非原始值，而是引用值。

对于希望复制一份独立的Buffer拷贝，Buffer提供了专门的方法

```
Buffer.copy(targetBuffer, [targetStart], [sourceStart], [sourceEnd]);
```

相对来说并没有直接的直接拷贝副本的方法，只能新建一个长度相等的Buffer，然后在原Buffer上调用copy方法，参数中还可以设置copy的启事与结束位置等。

![](http://blog.chinaunix.net/attachment/201403/25/26672038_1395758328p0MA.png)

对于更多的信息可以查看node的Buffer文档  [http://nodejs.org/api/buffer.html](http://nodejs.org/api/buffer.html)

## 什么时候才需要使用Buffer

好了，以上就是对Buffer的简单介绍，其实那都是虚的，什么时候才需要使用Buffer呢？

其实之前已经说过在Node中，许多地方的数据流均是使用的Buffer类型，以便于来处理一些二进制数据（比如读取文件数据，http请求中的post传递的数据等）。 

```
var fs = require('fs');
var rs = fs.createReadStream('chinese.md');
rs.on("data", function (chunk){
    console.log( Buffer.isBuffer( chunk ) )
});
```

![](http://blog.chinaunix.net/attachment/201403/25/26672038_1395758505cPtf.png)

从上面可知其均为 Buffer 类型片段，那么在一些情况下也会使得我们遇到一些隐藏的问题：

```
var fs = require('fs');
var rs = fs.createReadStream('chinese.md' , { highWaterMark: 5 } );
var data = '';
rs.on("data", function (chunk){
    data += chunk;
});
rs.on("end", function () {
    console.log(data);
});
```

![](http://blog.chinaunix.net/attachment/201403/25/26672038_1395758607bh1H.png)

会出现乱码现象，无法正常显示。

![](http://blog.chinaunix.net/attachment/201403/25/26672038_13957586459cC7.png)

一个中文字占了3个Buffer单位，但是在数据流分块的时候将一个中文的3个标识分开，再做 toString的时候救会发生出现这种乱码的情况。解决方式就是在拿到的分块数据千万不能直接进行类似toString式转义，将每段Buffer保存，最后合并成一个大的Buffer后在进行转义：

```
var fs = require('fs');
var rs = fs.createReadStream('chinese.md', { highWaterMark: 5 } );
var dataArr = [], len = 0, data;
rs.on("data", function (chunk){
    dataArr.push(chunk);
    len += chunk.length;
});
rs.on("end", function () {
    data = Buffer.concat( dataArr, len ).toString();
    console.log(data);
});
```

![](http://blog.chinaunix.net/attachment/201403/25/26672038_1395758710m428.png)

上述实例中我们可能还发现一个问题，将 { highWaterMark: 5 } 参数去掉后其实两个均没有问题，原因是因为默认的stream中切割data块单位是8K，所以可以在一个单位内容下这个小实例，所以没什么问题。一旦说数据很大，那么也会有相应的问题了。（之前一直拿txt测试了好久，发现怎么也不对，发现是编码方式问题）。

顺便说一下在拼接性能方面，其实Buffer与String相比并不是很差：

```
var buf = new Buffer('this is text concat test'),
	  str = 'this is text concat test';
console.time('buffer concat test');
var list = [];
var len= 100000 * buf.length;
for(var i=0;i<100000;i++){
    list.push(buf);
    len += buf.length;
}
var s1 = Buffer.concat(list, len).toString();
console.timeEnd('buffer concat test');
console.time('string concat test');
var list = [];
for (var i = 100000; i >= 0; i--) {
    list.push(str);
}
var s2 = list.join('');
console.timeEnd('string concat test');
```

![](http://blog.chinaunix.net/attachment/201403/25/26672038_13957587702EjN.png)

好吧，我承认写的有点不耐烦了，那么就这样吧，相当于简单的做个标签，有需要再查吧~ 哦吼吼~




