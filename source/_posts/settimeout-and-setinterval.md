---
title: setTimeout与setInterval
date: 2012-11-27
tags: setInterval,setTimeout
---

最近找了一些关于setTimeout与setInterval的一些资料，发现对于偏好各个都有。 但是我认为setTimeout与setInterval既然存在，总有他们自己的意义，过分的去深究到底孰优孰略是完全没有什么必要的，虽然本人更偏向setTimeout一些，但我绝对不会排斥setInterval。 我一般喜欢使用一段简单的代码来用 setTimeout 模拟 setInterval

```
function foo(){
    //... 执行代码
    setTimeout(foo,2000);
}
foo();
```

上述代码可以很好的模拟 setInterval 。 首先看一段实例：

```
function test(){
        var flog = +new Date();
        while(true){
                if(+new Date() - flog > 3000){
                        console.log("after");
                        console.log((+new Date()/1000).toFixed(0) + "s");
                        break;
                }
        }
}
function foo(){
            test();
            setTimeout(foo,2000);
}
foo();
```

test()中我使用while来占有内存，达到控制进程的效果（大量消耗内存，仅供测试）。下面为控制台的一段输出，间隔为5s。

```
after 
1354024132s 
after 
1354024137s 
after 
1354024142s
```

感觉是理所当然的，而 setInterval 就不一样了，

```
function test(){
        var flog = +new Date();
        while(true){
                if(+new Date() - flog > 3000){
                        console.log("after");
                        console.log((+new Date()/1000).toFixed(0) + "s");
                        break;
                }
        }
}
setInterval(test,2000);
```

其结果片段就不一样了，

```
after 
1354024417s 
after 
1354024420s 
after 
1354024423s
```

其间隔为3s。 上述为什么会有不一致性呢？ 这个可能需要从2个的根本区别说起， setTimeout() : 用于在指定的毫秒数后调用函数或计算表达式; setInterval() : 按照指定的周期(以毫秒计)来调用函数或计算表达式. 会不停地调用函数,直 到 clearInterval() 借用大牛的几句话：

1.  JavaScript引擎是单线程的，强制所有的异步事件排队等待执行
2.  setTimeout 和 setInterval 在执行异步代码的时候有着根本的不同
3.  如果一个计时器被阻塞而不能立即执行，它将延迟执行直到下一次可能执行的时间点才被执行（比期望的时间间隔要长些）
4.  如果setInterval回调函数的执行时间将足够长（比指定的时间间隔长），它们将连续执行并且彼此之间没有时间间隔。

举个例子： 我有一个需要执行1s的方法，我如果是 setTimeout 延时2s执行，那么从0开始 在 2 5 8···的时间点开始执行，而setInterval却在 2 4 6···时刻开始执行。 可以想象，当延时时间比执行时间短的时候 setInterval 会出现什么情况，当不断叠加不断消耗内存的风险。 总而言之，setTimeout 唯一存在的问题就是对于执行的时间点误差较大，但对于性能方面会比较友好，当然如果所需执行的为每一时间段，并且运行量较小，那就考虑选择 setIntervsl。