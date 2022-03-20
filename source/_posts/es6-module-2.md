---
title: 【译】 ECMAScript 6 模块化：最后的战役（下）
date: 2014-10-14
tags: 
- 前端开发
- 学习与方案
---
## 5. 导入导出更多信息

### 5.1. 导入

**ECMAScript 6 提供了以下的导入方式：**

```
// 定义式导出和命名式导出
import theDefault, { named1, named2 } from 'src/mylib';
import theDefault from 'src/mylib';
import { named1, named2 } from 'src/mylib';

// 重命名：导入named1 作为 myNamed1
import { named1 as myNamed1, named2 } from 'src/mylib';

// 作为兑现导入模块（每个命名式导出均作为一个属性）
import * as mylib from 'src/mylib';

// 仅仅加载模块而不导出
import 'src/mylib'
```

### 5.2. 导出

在当前模块内部有两种方式导出。一种就是以关键字 export 标记导出。

```
export var myVar1 = ...;
export let myVar2 = ...;
export const myVar3 = ...;

export function myFunc(){ ... }
export function* myGeneratorFunc(){ ... }
export class MyClass{ ... }
```

操作符 default 导出的是表达式（包括函数表达式，类表达式）。例如：

```
export default 123;
export default function (x){ 
    return x;
}
export default x => x;
export default class{
    constructor( x, y ){
        this.x = x;
        this.y = y;
    }
}
```

另一种方式就是将所有你想导出的列出来放置在模块最下方（风格与模块模式比较类似）。

```
const MY_CONST = ... ;
function myFunc(){
    ...
}

export { MY_CONST, myFunc };
```

也可以使用不同的名称导出：

```
export { MY_CONST as THE_CONST, myFunc as theFunc };
```

记住不能使用保留字作为变量名称（如 default 和 new），却可以作为导出名称来使用（在 ECMAScript 5 中也可以将其作为属性名称来使用）。如果你是直接导入这些命名式导出，那么你就需要使用变量名称来重命名。

### 5.3. 重导出

重导出意味着你在当前模块添加另一个模块的导出。你可以看添加所有的模块导出。

```
export * from 'src/other_moule';
```

或者你可以添加选着（通过重命名）：

```
export { foo, bar } from 'src/other_moule';
// 以 myFoo 来导出模块 other_module 的 foo
export { foo as myFoo, bar } from 'src/other_moule';
```

## 6. 模块的元数据

ECMAScript 6 也提供了在模块内部访问当前模块数据的方式（比如模块的 URL），如下：

```
import { url } from this module;
console.log( url );
```

this module 表示一个简单的作为一个模块导入元数据的标识。它也可以作为模块元数据。

也可以通过对象来访问元数据：

```
import * as metaData from this module;
console.log( metaData.url );
```

Node.js 使用模块局部变量 \_\_fileName 来作为这类元数据。


## 7. eval() 和 模块

eval() 不支持模块语法。它将参数按照脚本语法规则解析，而脚本是不支持模块语法的（稍后说明原因）。如果你想运行模块代码，你可以使用模块加载器的API（稍后说明）。
    
## 8. ECMAScript 6 模块加载器 API

除了定义了模块语法的工作之外，还有一个编程式的API，它可以使：

1. 编程式的使用模块和脚本
2. 配置模块加载。

加载器解决了模块修饰符（在 import... from 后面的字符串 ID）的加载模块。构造函数是  Reflect.Loader 。每个平台都有其自己特定的全局变量 System（系统加载器），实现其平台特定的模块加载方式。
    
### 8.1. 导入模块和加载脚本

你可以通过 ES6 promises 式的 API，编程式的导入一个模块：

```
System.import( 'some_moule' )
.then( some_module => {
    ...
} )
.catch( error => {
    ...
} )
```

System.import() 使你可以：

    1. 在 标签内部使用模块（不支持模块语法）。
    2. 限制性加载模块。

System.import() 可以载入单个模块，导入多个模块你可以使用 Promise.all():

```
Promise.all(
    [ 'module1', 'module2', 'module3' ]
    .map( x => System.import( x ) ) )
.then( {
    ...
} );
```

更多加载方式：

1. System.module( source, options? ) 在 source 的模块的中运行 JavaScript 代码（通过 promise 实现异步形式）
2. System.set( name, module ) 为注册一个模块（一个你通过 System.module() 创建的）。
3. System.define( name, source, option? )  运行模块代码并且注册结果。

### 8.2. 配置模块加载

模块加载器API的配置有各种钩子。完成工作依旧在进行中。第一个为浏览器开发的系统加载器当前正在实施测试。目标是找到最好的模块加载配置方式。

加载器API需要允许多种自定义加载，如：

1. 导入时检测模块是否符合Lint（如 通过 JSLint 或者 JSHint）
2. 导入时自动转译模块（模块内可能有 CoffeeScript 或者 TypeScript 代码）
3. 使用传统的模块（AMD，Node.js的）

配置模块加载在 Node.js 和 CommonJS 中是受限制的。

## 9. 蓝图

下文内容回答了两个关于 ECMAScript 6 模块的相关问题：如何运作？如何插入到HTML中？

1. “现在如何使用 ECMAScript 6”【译文在此】提供了一个 ECMAScript 6 的概述，解释了如何将其编译为 ECMAScript 5。如果有兴趣可以从[这里](http://www.2ality.com/2014/08/es6-today.html#using_ecmascript_6_today)开始阅读。一个小而有趣的解决方案 ES6 Module Transplier 仅仅添加了将 ES6 模块语法编译成 ES5，既不是 AMD 也非 CommonJS。
2. 在 HTML 中插入 ES6：在 script> 标签中的代码不支持模块语法，因为元素的同步特性与模块的异步特性是相冲突的。取而代之的你可以使用新的 标签。文章“[未来浏览器中的ECMAScript 6 模块化 ](http://www.2ality.com/2013/11/es6-modules-browsers.html)”介绍了 的工作原理。相对于 script>来说有几个明显的优点，并且可以选着替代版本