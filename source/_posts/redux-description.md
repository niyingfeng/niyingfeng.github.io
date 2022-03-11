---
title: Redux简单介绍
date: 2017-02-16
tags: React,Redux
---
最初知晓Redux这个概念就是在学习React的时候，简单的来说就是合理的来管理你react项目中的state变化操作。不过在开始时，一看其Action、Reducer、Store以及使用示例，表示真的会一脸懵逼。于是最近再比较详细的学习下Redux。

## Redux 简介

这里为最详细的官方文档 - [Redux官方文档](http://cn.redux.js.org//index.html)，这个文档介绍的非常非常详细，建议花大半天的时间与示例代码一起学习，基本就能完全理解Redux。

首先要说的是虽然很多人知晓Redux是在React的相关内容中，不过需要清楚，Redux与React确实是没有半毛钱关系，完全无依赖，均是独立的类库而已。就像你隔壁家的大婶的侄女和你大伯邻居家的儿子的关系一样，然而人家可以一见钟情那就是另外一回事了。

再说对于React来说其最大的优势就是严格控制了开发者对与DOM的直接操作，一直以来前端性能上很大的一个弊端就是开发者不合理以及频繁的去操作DOM，React以其高效的虚拟DOM对象、diff算法等，使得其以最优的方案来控制解决DOM的操作。

另外一个层面就是React将整个框架以及内部的组件视为一个个状态机，通过state来进行对虚拟DOM的控制以至来触发真正的DOM操作，不过这就会使得在一些复杂应用中带来另外一些问题，对于state的控制方面，越来越多的state，将在项目开发、版本迭代变中产生强相关和越来越不可控。这就意味着人为的去操作一个个state，渐渐开始变得像先前开发人员独自操作DOM一样让人难以忍受，就好像React变成了曾经它最痛恨的那种形式。杂乱的state操作最后就导致具体某一个state在某个时刻或者某个地方别改变，变得非常难以去查询。

相对于此问题的解决方案慢慢应运而生，Redux便是一种解决方案（但是相对来说Redux与React是没有关系的）。Redux强制在每一个应用中仅能使用整体单一的一个reducer生成唯一的一个store，来对state进行存储与操作，并且需要开发者独立定义各类形式的action，在触发某一action后通过所注册的reducer才能对某一状态进行操作。

简单的说就是将state的操作进行封装，禁止开发者随意使用，必须通过触发某一action操作，来触发相对应注册的reducer来对store内部相应的state进行操作。项目初期它不能提高我们对于项目的开发效率，不过后期以及项目维护阶段，它能使我们的项目大大增强了可维护性和可扩展性，使其变成一个真正健硕的项目工程。

## Redux 基础

### Action

action 意味着一个将数据中应用传递到store的操作定义，为一种对已发生事件的表达。纯粹的action仅仅只是一种定义，一般需要通过store.dispatch()实际出发才会生效。

内部的`type`字段为默认的定义Action类型的字段

```
{
  type: 'ADD_ITEM',
  text: 'add an item'
}
```

亦或创建Action函数,以便于更好的移植和复用

```
function addItem(text){
    return {
        type: 'ADD_ITEM',
        text
    }
}
```

这便可以称为一个Action，当然任意Action，需要被dispatch之后才会生效。

### Reducer

Action 只是对一个已发生行为进行描述，而Reducer则是处理触发了某一个实例Action进行更新state的一个过程的定义。简单的说就是某一个Action被触发之后，Action内部的数据会被带入到注册的Reducer内执行，根据Action的type类型进行对应的store变更操作。

首先，我们需要定义一个整体的state结构，这块内容需要在开发前进行思考组织。

```
{
  todos: [
    {
      text: 'item 1'
    },
    {
      text: 'item 2'
    }
  ]
}
```

随即，编写纯函数Reducer，来接受旧state，action，返回新的state。

纯函数意味着禁止在Reducer中做：

- 修改传入的参数
- 执行具有副作用的操作，如跳转，各类其他操作
- 调用非纯函数，如事件函数，随即函数等

```
const initialState = {
    todos: []
}

function todoAPP(state = initialState, action){
    switch(action.type){
        case 'ADD_ITEM':
            return {
                todos:[
                    ...state.todos,
                    {
                        text: action.text
                    }
                ]
            }
    }
}
```

对于实际应用中，对应的action数量会很多，在一个reducer中会使得switch结构维护性过差，所以都会使用到拆分reducer，最后使用combineReducers进行合并生成最终的reducer。


### Store

Redux应用只有唯一的store，通过应用唯一的最终Reducer生成而来。store是对state进行管理的中间件，当某一action被触发传入store的dispatch，触发注册的Reducer来改变store中对应的state。

```
const store = createStore(todoAPP)
```

store职责为：

- 维持应用的state
- 提供getState方法来获取state
- 提供dispatch方法来
- 提供subscribe进行注册事件
- 使用注册事件的返回值来注销事件


发起action，触发改变state
```
store.dispatch(addItem('item 3'))
```

整体来说，Redux的核心便是，控制开发者随意变更state，使用action来抽象具体的操作行为，由reducer生成的store进行对整体的state进行控制，在dispatch接受具体的action数据后来管理对于state的变更。

## Redux 思想

- 单一数据源，应用整体归结到一个state，存储应用在唯一的store中。state设计的扁平化，抽象化是确保其高效与可维护的的重要因素
- state为只读，触发action是唯一改变state的方式
- 纯函数来编写reducers，改变state

当然，还要包括异步的Action实现，Reducer的复用与性能，combineReducers高级用法等均可以在[中文文档](http://cn.redux.js.org/index.html)中获得答案，以及[GitHub](https://github.com/reactjs/redux)上有非常丰富的示例。