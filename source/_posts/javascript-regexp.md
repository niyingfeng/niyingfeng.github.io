---
title: JavaScript 正则简述
date: 2012-10-29
tags: 
- 正则表达式
- JS正则表达式
---

对于一种语言来讲，正则无疑是其中很重要的内容，由于正则对于新手来说并不是那么简单，它是较为复杂的模块。所以我们可以看到很多草率的正则表达式。所以很多情况我们在性能方面遇到的瓶颈问题都是由于并不理想的正则表达式引起的。 首先我们需要了解下正则表达式在`运行过程中的工作原理`：

1.  编译：当创建一个正则表达式时，浏览器会验证该模式，然后将其转化为一个原生的代码程序，用于执行匹配工作。
2.  设置起始位置：确认目标字符串起始的搜索位子，是由字符串的起始搜索位子或者正则表达式中的lastIndex属性决定的。
3.  匹配每个正则表达式字元：正则表达式会逐个检查文本和正则表达式模式，当摸一个匹配失败的时候会试着回溯到之前尝试匹配的位子上然后尝试其他匹配路径。
4.  匹配成功或者失败：如果正则表达式的所有可能路径都没有匹配到，那么会退回到第二步来从下个字符来进行匹配。

对于正则性能理解的关键在于理解`回溯的概念`。 每当正则表达式在匹配过程中，回溯是基础的组成部分。很大程度上也是正则表达式是否优越性的判定条件。但是一旦失控，就会产生巨大的系统资源消耗，对于整体的性能有着很大的影响。 每当正则表达式扫描字符串的时候，它从左到右来进行逐个测试组成部分，对每一个量词与分支都决定下一次走向，如有必要会记住其他的选着，以备返回的时候使用。如果当前匹配成功，正则表达式会继续扫描模式，如果不成功那么将回 到上一次最近的一个决策点，从另一个分支或者量词来重新进行匹配，直到成功。如果所有的匹配项都尝试已经没有匹配到，那么返回正则表达式不匹配的结果。 实例： /h(ello|appy) hippo/.test("hello there , happy hippo .");

1.  提取 h 进行匹配。
2.  提取 ello 进行匹配。
3.  提取 hippo进行匹配（匹配失败）。
4.  回到上一个决策点， h 之后的位子，提取 appy（匹配失败）。
5.  从第二个字符开始重新匹配整个表达式， 匹配 h （往后一个一个字符匹配）。
6.  匹配 ello （匹配失败）。
7.  匹配 appy。
8.  匹配 hippo（整体匹配成功）。

实例：

```
```
var str = "<p>p1</p><div>div1</div><p>p2</p>";
/<p>.*<\/p>/i.test(str);//由于会有贪婪匹配，所以会匹配到整个字符串
/<p>.*？<\/p>/i.test(str);//非贪婪匹配，则会匹配 <p>p1</p>
```
```

对于不了解贪婪匹配与非贪婪匹配的可以谷歌下。 然后是想一下回溯失控，

```
`/[\s\S]\*?[\s\S]\*?`
```