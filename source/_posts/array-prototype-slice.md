---
title: 关于Array.prototype.slice用途
date: 2012-09-03
tags:
- slice
---

遇到Array.prototype.slice也是在jQuery中，其toArray()就是使用的Array.prototype.slice.call来进行的处理。 那到底他的用途是什么呢？ 其实很简单，将像arguments那种的类数组对象转换为数组的形式。

```
Array.prototype.slice.call(arguments,0);
```

具体可以看以下代码

```
function curry(){
    alert('arguments:'+arguments);
    alert('typeof arguments:'+typeof arguments);
    alert('arguments instanceof Array:'+(arguments instanceof Array));
    var args = Array.prototype.slice.call(arguments,0);
    alert('args:'+args);
    alert('typeof args:'+typeof args);
    alert('args instanceof Array:'+(args instanceof Array));
}

curry("s","a","d");
```

从这里可以一目了然的看到起作用是很明显的，我们很多时候希望在函数中处理arguments，使其转换为数组形式更为简便明了。 还有一个问题就是在谷歌，火狐等浏览器下

```
Array.prototype.slice.call("abcd",0);
```

也会转变为数组，在IE下就会有误，为空。这个可能需要从原理上来解决。比较JS解析器是不一样的，目前我也没有什么具体答案。