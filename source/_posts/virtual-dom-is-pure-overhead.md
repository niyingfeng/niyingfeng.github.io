---
title: 【译】虚拟DOM只是纯粹的消耗
date: 2022-03-25 11:15:53
tags:
- 虚拟DOM
- Svelte
---

原文：[https://www.sveltejs.cn/blog/virtual-dom-is-pure-overhead](https://www.sveltejs.cn/blog/virtual-dom-is-pure-overhead)

作者：[Rich_Harris](https://twitter.com/Rich_Harris)

让我们摒弃虚拟DOM就是快速的神话。

如果近些年你使用过一些JS框架，你可能会听过 - 虚拟DOM就是快的代表，有时甚至让人感觉比真实DOM操作还要快。一些小伙伴还咨询 Svelte 是如何在不使用虚拟DOM的情况下变得快，这的确是一个令人惊奇的模型。

那么我们来仔细分析下。

<!-- more -->

### 什么是虚拟DOM？

在各类框架中，我们通过创建 render() 函数来构建一个app，比如下面简单的React 组件：

```jsx
function HelloMessage(props) {
  return (
    <div className="greeting">
      Hello {props.name}
    </div>
  );
}
```

你也可以不使用JSX来实现同等的逻辑：

```jsx
function HelloMessage(props) {
  return React.createElement(
    'div',
    { className: 'greeting' },
    'Hello ',
    props.name
  );
}
```

不过结果是一样的，为页面显示创建一个对象描述。这个对象就是虚拟DOM。每次当你的app的状态发生变更（比如当 name 的属性变化时），就会新建一个虚拟DOM。框架的工作就是协调新老虚拟DOM，去判定当前的变更是否需要同步更新到真实的DOM上。

### 模型是如何开始工作的？

对于虚拟DOM性能问题的误解可能需要追溯到React的发布期间。在 Rethinking Best Practices 中，前 React 核心团队成员 Pete Hunt 在 2013 年的一次演讲，有这么一段话：

> 它非常的快，主要由于很多DOM操作往往都过慢。虽然我们对于DOM做了大量的性能工作，但大多的DOM操作依旧会导致丢帧问题。
> 

![Screenshot from [Rethinking Best Practices](https://www.youtube.com/watch?v=x7cQ3mrcKaY) at JSConfEU 2013](https://image.freefe.cc/2022-03-25-1.png)

Screenshot from [Rethinking Best Practices](https://www.youtube.com/watch?v=x7cQ3mrcKaY) at JSConfEU 2013

但是，等下！虚拟DOM的操作只是对于真实DOM操作的补充扩展而已。真正的是它变得更快的原因只是我们将它和性能低的框架做比较而已（在2013年背景下，我们其实还有很多事情需要完善），或则就是做一些没人做过的事情 - 去反对一个不会反驳的点：

```jsx
onEveryStateChange(() => {
  document.body.innerHTML = renderMyApp();
});
```

随后Pete就澄清了...

> React 并非魔法。就像你可以使用 C 语言来编写汇编程序来超越 C 的编译器一样，你也可以使用操作原始 DOM 和调用 DOM API 来超过 React。但是使用 C 或者 Java 或者 JavaScript 是一个数量级的性能提升改进，因为你并不需要担心关于平台的一些细节。在 React 下你可以在不关心任何性能问题的情况下来来构建你的应用程序，因为默认的状态就是最快的。
> 

但这并非问题点。

### 那么，虚拟DOM是否真的慢呢？

不完全是。通常情况下虚拟DOM是足够快的，但也有一些特殊情况。

React 原本的承诺是可以在每次单个状态变更后重新渲染整个应用，而无需关系性能问题。但实践中我认为这并不准确。如果真是这样，就没必要有类似 `shouldComponentUpdate` 之类的优化（告知React如何安全的跳过组件重新处理的方式）

即使有了 `shouldComponentUpdate`，一次性更新你整个应用的虚拟DOM依旧是一个很大的工作量。不久前（本文为18年文章），React 团队引入了React Fiber的机制，其允许将更新拆解为小的chunks来运作。这意味着（对于其他事务）更新尽管不会减少总的工作量和时间，但是至少不会阻塞主线程。

### 性能开销从何而来？

显而易见，处理diff并非无成本。如果你不将新的虚拟DOM和老的虚拟DOM快照做对比就无法将变化更新至真的的DOM。回到刚刚 `HelloMessage` 的例子，如果 name 属性从 world 变为 everybody。

1. 两个快照都含有一个唯一的元素，均为一个 div，那么我们就可以使用相同的 DOM 节点。
2. 枚举新老 div 的所有属性，观察是否有变化，以便添加或者移出。这两边我们看到都还有一个唯一的属性 className 为 greeting的值。
3. 到元素内部，我们看到text发生了变化，所以我们需要更新真实的DOM。

在这三步中，在这个case中仅第三步是具有价值的，基础的ap结构并未发生任何变化。如果我们能直接跳至第三步那么效率将相当的高：

```jsx
if (changed.name) {
  text.data = name;
}
```

### 不仅仅是处理 diffing 的问题

使用在 React 和其它虚拟DOM框架上的 diffing 算法是非常高效的，但是更大的开销在于组价本身。比如你不应该这样写代码：

```jsx
function StrawManComponent(props) {
  const value = expensivelyCalculateValue(props.foo);

  return (
    <p>the value is {value}</p>
  );
}
```

因为你会使得每次变更时都会重新计算 value，而不管是否 props.foo 发生了变化。类似很多的没必要的计算和分配非常的常见：

```jsx
function MoreRealisticComponent(props) {
  const [selected, setSelected] = useState(null);

  return (
    <div>
      <p>Selected {selected ? selected.name : 'nothing'}</p>

      <ul>
        {props.items.map(item =>
          <li>
            <button onClick={() => setSelected(item)}>
              {item.name}
            </button>
          </li>
        )}
      </ul>
    </div>
  );
}
```

比如这边，我们生成了一个新的虚拟 li 元素的列表，每一个元素都含有自己的内敛的事件方法，当每一次状态变更，不管 porps.items 是否变化。除非你特别痴迷于性能，一般你不会尝试去优化它。因为毫无意义。已经足够快了。但是你知道怎么做更快么？就是什么都不做。

去做不必要工作的风险，即使内容微不足道，如果没有明确的优化时间，那你在项目上总会屈服与乱七八糟的各类小问题。（可以预测，React Hooks 其实做了加倍的没有意义的一些工作）

Svelte 明确的来帮助你解决这类情况。

### 为什么框架需要使用虚拟DOM？

明白虚拟DOM并非一类feature很重要。这仅仅是对声明式和状态驱动的UI开发的目的的方式。虚拟DOM的价值在于其允许你在构建应用的时候无需关心状态的转换，并且一般情况下性能也是满足需求的。那这意味着更少的问题代码，更多的创造性任务替代乏味性的任务。

事实证明，我们可以实现一个类似的编程模型但抛开虚拟DOM，这就是Svelte的意义。