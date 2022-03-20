---
title: 编写可维护性的前端代码
date: 2018-05-23
tags: 
- 可维护性
- 高质量代码
---
# 编写可维护的 JavaScript 读书笔记

**尼古拉斯（作者）**： 前端大牛工程师，目前在 Box 公司任职，之前是在雅虎将近工作 5 年。在雅虎期间，他是雅虎首页的前端技术主管，并且是 YUI 库的贡献者。 Nicholas 编写的技术书有：《Maintainable JavaScript | 编写可维护的 JavaScript》、《Professional JavaScript for Web Developers | JavaScript高级程序设计》、《High Performance JavaScript | 高性能JavaScript》、《Professional Ajax》。

**任何开发者都不在乎某个文件的作者是谁，也没有必要花费额外的精力去理解代码的逻辑并且重新排版，因为所有代码的排版风格都是非常一致的，因为风格不一致会导致我们打开代码文件的第一件事情不是立即工作，而是进行缩进的排版整理。**

**能很容易的辨别出有问题的代码并且发现错误，如果所有的代码风格很像，当看到一段与众不同的代码时，很肯能问题就出在这里。**
 
>   “程序是写给人读的，只是偶尔让计算机执行一下” - Donald Knuth



## 基本的格式化

**1.1 缩进层级**  
建议4个空格，或在编辑器中设置一个 制表符 替换为 4个空格。

**1.2 语句结尾**  
强制规定，语句结束插入分号。

**1.3 行的长度**  
很多语言都建议行的字数不超过 80 字

**1.4 换行**

```
// 百度代码规范
// good
if (user.isAuthenticated()
    && user.isInRole('admin')
    && user.hasAuthority('add-admin')
    || user.hasAuthority('delete-admin')
) {
    // Code
}

var result = number1 + number2 + number3
    + number4 + number5;
```

**1.5 空行**  

如何通过空行让代码感觉更透气

    1. 方法之间  
    2. 方法中局部变量和第一条语句之间。  
    3. 多行或者单行注释之前。  
    4. 在方法内的逻辑片段之前。

**空行常常被忽略，但是空行也是提高代码可读性的强大武器。**
    
```
if(wl && wl.length){
    for(var i=0; i<wl.length; i++){
        p = wl[i];
        type=Y.Lang.type(wl[i]);
        if(a){
            ...
        }
    }
}

if(wl && wl.length){

    // 循环的业务逻辑注释
    for(var i=0; i<wl.length; i++){
        
        // 赋值逻辑注释
        p = wl[i];
        type=Y.Lang.type(wl[i]);
        
        // 条件判定逻辑注释
        if(a){
            ...
        }
    }
}
```
    
**1.6 命名**
    
1 变量和函数  
变量名总是应该遵循驼峰大小写命名法，并且命名的前缀应当是名词，使得可以将变量和函数区分开。

```
// 以下OK
var count = 10;
var myName = "Ni";
var found = true;

// 以下就需要重新命名
var getCount = 10;
var isFound = true;

//函数应当以动词开头
var getName  = function(){
    return myName;
};
```

还有一点示范  

    can        函数返回布尔值
    has        函数返回布尔值
    is         函数返回布尔值
    get        函数返回非布尔值
    set        函数用来保存值


2 常量  
使用大写字母并且使用下划线命名

3 构造函数  
大驼峰命名法

1.7 直接量

## 注释

**2.1 单行注释**  

    1. 独占一行，用来解释下一行，并且这行注释之前总有一行空行，缩进的层级与下一行代码保持一致。  
    2. 在代码行的尾部注释。代码与注释之间至少有一个缩进。注释包括之前本行的代码不应该超过最大字符数限制。  
    3. 注释掉大块代码。  
    
    
```
    // 好的注释方法
    if ( condition ) {
        
        // 解释下一行代码
        func();
    }

    if ( condition ) {
        func(); // 解释该代码功能
    }
    
    if ( condition ) {
    
        // 多行解释代码
        // 多行解释代码
        func();
    }
```

**2.2 多行注释**

**2.3 使用注释**   

    1. 当代码不够清晰的时候使用注释，当代码很明了的时候不应当添加注释。  
    2. 注释不应该只是解释变量名称或者函数名称式的那种废话。  
    3. 当代码很难理解或者代码可能被误认为错误的代码的时候，需要添加注释。  
    
**2.4 文档注释**   

技术角度来说，文档注释并不是JS的组成部分，但是是一种普遍的实践。  

    1. 应当对所有的方法和可能的返回值添加描述。  
    2. 对自定义的构造函数类型和期望的参数添加描述。  
    3. 对于包含对象或者方法的对象进行描述。


## 语句和表达式

**3.1 花括号对齐方式**

**3.2 块语句间隔**  

语句块间隔有3中形式：  

    1. 语句名 圆括号 左花括号间没有空格间隔。  
    2. 左圆括号之前 右圆括号之后添加一个空格。  
    3. 左圆括号 右圆括号前后都添加一个空格。 

**3.3 switch 语句 缩进&case语句的连续执行（贯穿）**  
JS的switch缩进一致是有争议的话题。

    1. 每个case相对于switch都有一个缩进层。  
    2. 第二个case开始之前都有一个空行。

```
switch (condition) {
    case "a":
        //代码
        break；

    case "b":
        //代码
        break;

    default :
        // 代码
}
```
  
另一种 Crockford 和Dojo编程风格为  
```
switch (condition) {
case "a":
    //代码
    break；
case "b":
    //代码
    break;
default :
    // 代码
}
```

```
switch (condition) {
    case "a":
    case "b":
        //代码
        break;

    default :
        // 代码
}
```
Douglas 在 Crockford中禁止出现贯穿现象，但是Nichlas认为只要程序逻辑很清楚，贯穿完全是一个可以接受的编程方式。Jquery 编程风格中也是允许的。


**3.4 with**

**3.5 for 循环**  

对于for循环中的break和return会改变循环的方向，尽量避免使用，但是不禁止。

**3.6 for-in 循环**  

for-in循环建议与 hasOwnProperty 一起检测属性，除非你需要检查原型。


## 变量，函数和运算符

**4.1 变量声明**  

变量声明是通过 var 语句来完成的。变量声明不管是在何处声明，所有的 var 语句都会提前到代码顶部执行。

由而产生了一种单一的 var 风格声明。
尼古拉斯推荐 合并var声明，可以清晰看到所有变量名 以及缩小代码量。并且建议每个变量都独占一行，统一缩进。
```
function doSomeThing (item){
    var i,
    len,
    value = 10,
    result = value+10;
    
    for(i=10, len=item.length; i<len; i++){
        do(item[i]);
    }
}
```


百度JS规范

    [强制] [RULE071] 每个 var 只能声明一个变量。  
    [强制] [RULE072] 变量必须即用即声明，不得在函数或其它形式的代码块起始位置统一声明所有变量。

```
// good
function kv2List(source) {
    var list = [];

    for (var key in source) {
        if (source.hasOwnProperty(key)) {
            var item = {
                k: key,
                v: source[key]
            };
            list.push(item);
        }
    }

    return list;
}

// bad
function kv2List(source) {
    var list = [];
    var key;
    var item;

    for (key in source) {
        if (source.hasOwnProperty(key)) {
            item = {
                k: key,
                v: source[key]
            };
            list.push(item);
        }
    }

    return list;
}
```

**4.2 函数声明**

推荐总是先声明函数，再使用函数。Crockford规范还推荐在函数内部的函数应当紧跟着变量声明之后声明。

```
function doSomeThing (item){
    var i,
        len,
        value = 10,
        result = value+10;
    
    function do(item){
        ...
    }
    
    for(i=10, len=item.length; i<len; i++){
        do(item[i]);
    }
}
```

函数声明不应当出现在语句块之内，会有兼容方面的问题导致各个浏览器解析不一致。也是google规范中禁止的。


**4.3 函数调用隔离**

代码比较透气，增强可读性和读代码的舒适度

百度代码规范 [空格](http://styleguide.baidu.com/style/js/index.html#id6)

```
var a = !arr.length;

if (condition) {
}

var obj = {
    a: 1,
    b: 2,
    c: 3
};

function funcName() {
}

var funcName = function funcName() {
};

funcName();

callFunc(a, b);
```

**4.4 立即调用的函数**

**4.5 严格模式**  
不推荐将严格模式使用在全局之中

**4.6 相等**

```
5 == "5" // true
25 == "0x19" // true
2 == true // false
```

对于对象，则会首先调用对象的 valueOf() 方法，得到原始类型值比较，如果没有定义则调用 toString()。
```
var object ={
    toString: function(){
        return "0x19";
    }
}
object == 25; // true
```
由于强类型转换的情况，所以需要使用=\==和!\==来处理比较的情况。而不推荐使用==和!= 。



## 编程实践

> 构建软件设计的方法有两种：一种是把软件做的很简单以至于明显找不到缺陷，另一种是把它做的很复杂以至于找不到明显的缺陷。 - C.A.R.Hoare

**5. 松耦合**

很多的设计模式就是为了解决紧耦合的问题。如果2个组件耦合太紧密，就说明一个组件和另一个组件直接相关。

比如有一个名为error的className，贯穿整个站点。有一天觉得他命名不合理需要更改的时候，有可能我们需要做更改上百个使用到它的组件，也有可能只需要更改一个组件。 

你能做到只修改一个组件而不是说修改N个组件，那么就做到了松耦合。对于一些大型系统来说这是至关重要的。

## 避免使用全局变量

JS执行环节相对于其他语言来说有很多独特之处，如全局变量和函数的使用。 

JS本身的初始执行环境就定义了多种多样的全局变量，这些全局变量在环境创始之初就存在了。  

全局对象是一个神秘的对象，表示脚本的最外层上下文。  

**6.1 全局变量带来的问题**

    命名冲突  
    代码的脆弱性  
    难以测试  
    
**6.2 意外的全局变量**

```
function doSomething(){
    var count = 10;
        title = "abcdefg";

    var a = b = 0;
}
```
对于意外的全局变量一些工具，比如JSLint和JSHint就可以起到作用,严格模式下也会报错。

**6.3 单全局变量方式**

YUI 引入 唯一 YUI全局变量。  
jQuery 引入 $ 和 jQuery 全局变量。  
Dojo 引入 dojo 全局变量。  
Closure 引入 goog 全局变量。  

    命名空间  
    模块

**6.4 零全局变量**


## 事件处理

事件处理在JS中是至关重要的，影响着网站的各个方面。所有的JS代码均通过事件绑定到UI上，所以大多前端工程师需要花费很多的事件来编写和修改事件处理程序。  

大多事件处理程序相关代码和事件环境紧紧偶合在一起，导致了可维护性很糟糕。

```
// 不好的例子
function handleClick( event ){
    var popup = document.getElementById( "popup" );
    popup.style.left = event.clientX + "px";
    popup.style.top = event.clientY + "px";
    popup.className = "reveal";
}
addListener(element, "click", handleClick);

```

*规则1：隔离应用逻辑*  
将应用逻辑从所有的事件处理程序中抽离出来是一种最佳实践。因为很有可能在之后的某段代码中我们会使用到同一段逻辑，抽离就降低了代码的耦合度，增强了可读性和维护成本。

```
var MyApplication = {
    handleClick : function(event){
        this.showPopup( event )；
    } ,
    
    showPopup : function(event){
        var popup = document.getElementById( "popup" );
        popup.style.left = event.clientX + "px";
        popup.style.top = event.clientY + "px";
        popup.className = "reveal";
    }
}

addListener(element, "click", function(event){
    MyApplication.handleClick( event );
});
```

*规则二：不要分发事件对象*
```
var MyApplication = {
    handleClick : function(event){
        this.showPopup( event.clientX, event.clientY )；
    } ,
    
    showPopup : function(x, y){
        var popup = document.getElementById( "popup" );
        popup.style.left = x + "px";
        popup.style.top = y + "px";
        popup.className = "reveal";
    }
}

addListener(element, "click", function(event){
    MyApplication.handleClick( event );
});
```

清楚地展示了事件处理程序和应用逻辑之间的分工。应用逻辑也不需要对event产生任何依赖，进而很多地方都可以使用相同的应用逻辑。

## 比较

**8.1 检测原始值**

5种原始类型 字符串、数字、布尔值、null和undefined。  
typeof 可以检测各个原始值的类型，并且返回相应类型字符串。而 typeof null 会返回 "object"。

**8.2 检测引用值**

对于检测引用值，typeof会力不从心，基本都会返回"object"，那么对于引用值检测最好的方法是使用 instanceof。

```
if( value instanceof Date ){
    // ... Code
}
if( value instanceof RegExp ){
    // ... Code
}
if( value instanceof Error ){
    // ... Code
}
```

**8.3 检测属性**

很多时候我们会使用与 null  undfined 对比来判断是否属性存在。
```
if( obj[prop] ){  }
if( obj[prop] != null ){  }
if( obj[prop] != undefined){  }
```
这样会导致代码有漏洞，不能将所有的情况都覆盖到。


## 将配置数据从代码中分离

代码就是一些计算机运行的指令，当我们传递数据进入计算机的时候，指令对数据进行操作产生结果。  

那么当我们在修改一些数据问题的时候就会带来一些源代码引入BUG的风险，所以对于应用来说，应当将一些关键数据从主要的源码中抽离出来，这样就可以是我们在修改数据或者源码的时候更放心。

**9.1 什么是配置数据**

配置数据就是我们在应用中写死（hardcoded）的值。
配置数据的例子：

    1. URL
    2. 需要展示给用户看的字符串
    3. 重复的值
    4. 设置的值，一些配置选项
    5. 任何可能发生变化的值

配置数据随时都有可能做修改，我们不希望因为有人需要修改页面的提示信息而需要修改JS源码。

**9.2 抽离配置数据**

其实，抽离配置数据非常简单，只需要创建一个管理整体的配置数据的对象即可。
```
var config = {
    MSG_INVALID_VALUE : "Invalid value",
    URL_INVALID : "/errors/invalid.html"
}
```
注意统一在配置数据对象中的命名。  
将配置数据提取出来之后任何人需要做修改都不会引起一些代码的逻辑错误问题，对于很大的系统，可以将配置数据专门使用其他单独文件，使其于代码隔离。

**9.3 保存配置数据**

配置数据最好放在单独的文件之中，以便清晰地分隔数据和应用逻辑。一种值得尝试的方法是将这些配置数据放于非JS的文件中。

    1. 使用各种流行的属性文件来存放。（如 Java属性文件）
    2. JSONP，将JSON结构使用函数调用包装起来。
    3. 纯JS对象
    
## 抛出自定义错误

编程语言具有“创建”错误的能力，在JS中抛出错误是一门艺术。再合适的时间抛出错误可以大大减少我们调试的事件，对代码的满意度也将急剧提升。

**10.1 错误的本质**

当某些非期望的事情发生的时候，程序就会要发一个错误。这也许是传入了非法的值，或许是遇到无效的操作符等等。

编程语言定义了一组基本的规则，当偏离了这些规则的时候将导致错误，只有当错误被抛出来时，我们才能有地方入手解决，如果错误无声无息，那么解决的代价可想而知。所以错误时开发者的朋友而非敌人。

**10.2 在 JS 中抛出错误**

毫无疑问，在 JS 中抛出错误比任何语言中做同样的事情更有价值，这要归结于Web端调试的复杂性。可以使用 throw 操作符，将提供一个对象作为错误抛出，Error对象时最常用的。

**10.3 抛出错误的好处**

```
function getDivs( element ){
    return element.getElementByTagName("div");
}

function getDivs( element ){
    if( element && element.getElementsByTagName ){
        return element.getElementByTagName("div");
    } else {
        throw new Error("getDivs() : Argument must be a DOM element");
    }
}
```

**10.4 何时抛出错误**
```
// 不好的写法 检测太多
function addClass(element, className){
    if( !element || typeof element.className != "string" ){
        throw new Error("addClass() : First argument must be a DOM element");
    }
    if( typeof element.className != "string" ){
        throw new Error("addClass() : First argument must be a DOM element");
    }
    element.className +=" " + className;
}
// 原本只是需要加一个class 但是加了那么多的抛出错误，就会适得其反。会引起过度杀伤。
// 好的写法
function addClass(element, className){
    if( !element || typeof element.className != "string" ){
        throw new Error("addClass() : First argument must be a DOM element");
    }
    element.className +=" " + className;
}
```

    1. 当修复了一个难以调试的错误的时候，尝试增加一两个自定义错误，当再次发生错误的时候有助于解决问题。
    2. 如果正在编写代码，思考一下我希望哪些事情不会发生，如果发生将引发较大问题，在这个某些问题上抛出错误。
    3. 如果在编写别人的代码，思考下他们的使用方式在特定的情况下抛出错误。


**10.5 try-catch 语句**

try-catch 可以在浏览器抛出错误之前解析，将可能引发错误的代码放在 try 块中，将处理错误的代码放在 catch 中。当然还可以加入 finally 块。

在catch中不要为空，应该总要写点声明老处理错误，不然就会依旧不知道错误在哪里。

**10.6 错误的类型**

    Error ： 所有错误的基本类型，引擎不会抛出此类型。  
    EvalError ： 通过 eval() 函数执行的代码发生错误。  
    RangeError ： 一个数字超出边界时抛出，试图创建一个长度为-20的数组。 
    ReferenceError ： 期望对象不存在，例如试图在null上调用函数。  
    SyntaxError :  语法上的错误。  
    TypeError ： 变量不是期望类型的时候。  
    URIError ： 给一些内置函数传入非法URL时抛出的错误。


## 不是你的对象不要动

**11.1 什么是你的**

当你的代码创建了这些对象，那么你拥有这些对象。维护是你的责任。牢记，如果你的代码没有创建这些对象，那么不要修改它们。

    1. 原生对象。
    2. DOM对象    
    3. 浏览器对象模式对象（BOM）。   
    4. 类库对象。

**11.2 原则**

在JS中，我们将已经存在的对象视为一种背景，在此之上开发代码，应该将JS对象当做一个实用的工具函数来看待。

不覆盖方法，神圣的 document.getElementById 都可以被轻易覆盖。  
不增加方法，Prototype库就是一个不好的例子，很随意的修改了大量的原生对象和方法。导致在JS的历史上遇到很多问题。在小于1.6的版本中，Prototype定义了一个document.getElementsByClassName()方法  
不删除方法。

**11.3 更好的途径**

修改非自己拥有的对象是解决某些问题的很好的方案。可能有一些方法，所谓的设计模式，不直接修改这些对象而是扩展这些对象。

最受欢迎的对象扩充方式就是继承。一种类型的对象如果已经做到了你想要的大多数工作，那么继承，再加一些新的功能即可。JS中有两种基本形式：基于对象的继承和基于类型的继承。

*11.3.1 基于对象的继承*

基于对象的继承也就是原型继承，即ES5中的 Object.create() 方法。
```
var person = {
    name : "Nicholas",
    sayName : function(){
        alert( this.name );
    }
}
var ni = Object.create( person );
// 当然可以扩展对象
var ni = Object.create( person， {
    name : {
        value : "Greg"
    }
} );
```
一旦以这种方式创建了对象，那么你可以随意修改新对象，毕竟你是对象的拥有者。
    
*11.3.2 基于类型的继承*

基于类型的继承是通过构造函数而非对象
```
function MyError( message ){
    this.message = message;
}
MyError.prototype = new Error();
```

**11.3.3 门面模式**

门面模式是一种流行的设计模式，他为一个已经存在的对象创建新的接口，门面模式是一个全新的对象，背后是一个已经存在的对象在工作。

如果你的用例中无法使用继承满足需要，可以使用门面模式。

```
funtion DOMWrapper( element ){
    this.element = element;
}
DOMWrapper.prototype.addClass( className ){
    this.element.classmName += " " + className; 
}

var wrapper = new DOMWrapper( document.getElementBuId("myDiv") );
wrapper.addClass("selected");
```

从JS的可维护性来说，门面模式是非常合适的方式，你可以完全控制这些接口。你可以允许访问任何底层对象的方法或者属性，反之也可以有效地过滤对象的访问。
门面实现一个特定的接口，让一个对象看上去像另一个对象就是适配器。两者的差别就是前者创建接口，后者实现已经存在的接口。

**11.4 关于Polyfill的注解**

随着ES5和H5的特性在各个浏览器中实现，JS polyfills（shims）开始就行起来，polyfill是指一种功能的模拟，这些功能在新版本的浏览器中已经实现，然后用自定义的方式使其在老版本中兼容实现。

**11.5 阻止修改**

在ES5中已经引入了几个方法来防止对对象的修改。
有三种级别： 防止扩展， 密封， 冻结。


## 浏览器嗅探

浏览器嗅探始终是web领域的一个热门话题。

**12.1 User-Agent 检测**

用户代理检测是更具客户端浏览器的user-agent字符串进行检测，但是最大的问题就是解析user-agent并不是很容易，有些浏览器为了保证兼容性，会复制另一个浏览器的user-agent字符串，每当一个新的浏览器出来的时候我们都需要对user-agent检测的代码进行重新修正。意味着我们无法预期的出了问题，只能等待问题出现后再进行处理。

**12.2 特性检测**

特性检测的原理就是为特定浏览器的各个特性进行检测，并当特性存在时处理问题。
```
function getById( id ){

    var element = null;

    if( document.getElementById ){
        element = document.getElementById( id );
    }else if( document.all ){
        element = document.all[id];
    }else if( document.layers ){
        element = document.layers[id];
    }
    
    return element;
}
```

特性检测的重要组成部分

    1. 探测标准方法
    2. 探测不同浏览器的特点方法
    3. 当被检测方法不存在的时候提供合乎逻辑的备用方案。
    
**12.3 避免特性推断**

特性推断是更具一个特性的存在，推断另外一个特性是否存在。“如果他看起来像鸭子，就必定会像鸭子一样嘎嘎叫”

**12.4 避免浏览器推断**

**12.5 如何取舍**

特性推断和浏览器推断是非常糟糕的做法，应当不惜一切代价避免他，纯粹的使用特性检测是一种很好的方式，几乎在任何情况下都是你想要的结果。

对于 user-agent 检测，从来都不禁止他，因为总有非常适合的场景需要使用他。



# React的一点学习

## 容器型组件&展示型组件

容器型组件：意味组件如何工作，具体数据如何更新，不包含任何Virtual DOM的修改与组合，不包含组件样式。
展示型组件：意味组件是如何渲染的，包含了Virtual DOM的修改与组合。不依赖store。


这样区分能较好的使用相同的展示型组件来配合不同的数据最渲染，提高复用性。

Layouts
页面布局结构，讲主框架与页面主体内容分离。

```
const Layout = ({children}) => (
    <div className="container">
        <Header />
        <div className="content">
            {children}
        </div>
    </div>
);
```

Views
字路由的入口组件，包含此路由下的所有展示型组件。为保持子组件的纯净，在此层中定义数据以及action等，分发到子组件中。

```
class HomeView extends Component {
    render() {
        const { sth, changeType } = this.props;
        const cardProps = { sth, changeType };
        
        return (
            <div className="page page-home">
                <Card {...cardProps} />
            </div>
        );
    }
}
```

Components
渲染组件，包含相关组件的业务逻辑与交互，但所有数据与action均为Views下发，完全脱离数据层存在的展示型组件。项目中数量级别较大。


```
class Card extends Component {
    constructor(props) {
        super(props);
        
        this.handleChange = this.handleChange.bind(this);
    }
    
    handleChange(opts) {
        const { type } = opts;
        
        this.props.changeType(type);
    }
    
    render() {
        const { sth } = this.props;
        
        return (
            <div className="mod-card">
                <Switch onChange={this.handleChange}>
                    //...
                </Switch>  
            </div>
        );
    }
}
```
## 组件间抽象

在React组件构建中，对于一类需要被不同需求同时使用的组件，就需要设计组件抽象的概念。主要为mixin与高阶组件。

高阶函数：函数接受函数作为输入，或者输出一个函数。
高阶组件：接受React组件作为输入，输出一个新的React组件。

```
const MyContainer = (WrappedComponent) => 
    class extends Component {
        render() {
            return <WrappedComponent {...this.props} />
        }
    }
```

当React组件被包裹，高阶组件返回一个增强型的React组件，使得代码更具有复用性、逻辑性与抽象特性。


抽象一个input组件

```
const MyContainer = (WrappedComponent) => 
    class extends Component {
        consttructor(props) {
            super(props);
            this.state = {
                name: ''
            }
            
            this.onNameChange = this.onNameChange.bind(this);
        }
        
        onNameChange(event) {
            this.setState({
                name: event.target.value
            })
        }
        
        render() {
            const newProps = {
                name: {
                    value: this.state.name,
                    onChange: this.onNameChange
                }
            }
            
            return <WrappedComponent {...this.props} {...newProps}/>
        }
    }
    

@MyContainer
class MyComponent extends Component {
    render() {
        return <input name="name" {...this.props.name} />
    }
}
```

简单的评论功能页，包含评论内容区域以及评论编辑区域

```
// 数据类型
{
    commentList: [
        {name: "cam", content: "It's good!", publishTime: "2015-01-01"}
    ]
}

class  CommentList extends Component {
    constructor(props) {
        spuer(props);
        
        this.state = {
            loading: true,
            error: null,
            value: null
        }
    }
    
    componentDidMount() {
        this.props.promise.then(respones => reponse.json())
            .then(value => this.setState({loading: false, value}))
            .catch(error => this.setState({loading: false, error}))
    }
    
    render() {
        if (this.state.loading) {
            return <span>loading...</span>
        } else if (this.state.error !== null) {
            return <span>Error:{this.state.error.message}</span>
        } else {
            const list = this.state.value.commentList;
            
            return (
                <ul className="comment-box">
                    {list.map((entry, i) => (
                        <li key={`response-${i}`} className="comment-item">
                            <p className="comment-item-name">{entry.name}</p>
                            <p className="comment-item-content">{entry.content}</p>
                        </li>
                    ))}
                </ul>
            );
        }
    }
}

// 使用

<CommentList promise={fetch('/api/response.json')}>

```

显然数据请求与业务逻辑混合在一起了，那么此时应该具体的确认区分容器型组件与展示型组件的界限。
并且对于公共逻辑的抽象。

```
// 展示型组件
class CommentList({commens}) {
    return (
        <ul className="comment-box">
            {commens.map((entry, i) => (
                <li key={`response-${i}`} className="comment-item">
                    <p className="comment-item-name">{entry.name}</p>
                    <p className="comment-item-content">{entry.content}</p>
                </li>
            ))}
        </ul>
    );
}

// 高阶函数 抽象异步加载的模块的结构
const  Promised = (promiseProp, Wrapped) =>class extends Component {
    constructor(props) {
        spuer(props);
        
        this.state = {
            loading: true,
            error: null,
            value: null
        }
    }
    
    componentDidMount() {
        this.props[promiseProp].then(respones => reponse.json())
            .then(value => this.setState({loading: false, value}))
            .catch(error => this.setState({loading: false, error}))
    }
    
    render() {
        if (this.state.loading) {
            return <span>loading...</span>
        } else if (this.state.error !== null) {
            return <span>Error:{this.state.error.message}</span>
        } else {
            const list = this.state.value.commentList;
            
            return <Wrapped {...this.props} {...this.state.value} />
        }
    }
}

class  CommentListContainer extends Component {
    render() {
        return <CommentList comments={this.props.commentsList} />
    }
}

module.exports = Promised("comments", CommentListContainer);

// 使用
<CommentListContainer comments={fetch('/api/response.json')} />

```
CommentListContainer 与 CommentList 几乎等同，可以选择合并或者保留。取决于对于未来扩展的考量。


![image](http://image.freefe.cc/Snipaste_2018-04-11_10-12-32.png)



## 组件性能优化

纯函数：
给定相同的输入，总返回相同的输出
过程没有副作用
没有额外的状态依赖

完全独立于外部状态，避免因为共享的外部状态而导致BUG。并且有利于方法级别的测试与重构。

PureRender：将props与state做浅比较（对象仅作引用比较），避免深比较的性能问题。

Immutable：一旦创建，则无法更改的对象，每次操作会生成新的 ImmutableData。
1. 降低可变带来的复杂度
2. 节省内存（简化垃圾回收机制）
3. 撤销，复制，时间旅行等实现容易
4. 并发安全
5. 拥抱函数式编程

key: 标识当前唯一项的props


## 无状态组件
类似纯函数形式，仅简单的接受props渲染生成DOM结构。纯粹未渲染而生的组件。简单便捷与高效。

# ES6语法的优雅写法

## 块级作用域

一直以来 var 声明让开发者在循环中创建函数以及一些循环事件绑定时，变成容易出错，陷入闭包的问题中。

```
var funcs = [];

for (var i = 0; i < 10; i++) {
    funcs.push(function(){
        console.log(i);
    });
}

funcs.forEach(function(func){
    func();
})
```

不得已，需要再创建一层作用域来解决此类问题，就显得不那么优雅

```
var funcs = [];

for (var i = 0; i < 10; i++) {
    funcs.push((function(value){
        return function(){
            console.log(value);   
        }
    })(i));
}

funcs.forEach(function(func){
    func();
})
```

而块级作用域 let声明解决了该问题，每次迭代均会生成新变量。

```
let funcs = [];

for (let i = 0; i < 10; i++) {
    funcs.push(function(){
        console.log(i);
    });
}

funcs.forEach(function(func){
    func();
})
```

那么新的标准下，最佳实践为默认使用 const，在变量确实需要改变的时候使用 let。

## 模板字面量

提供了一套生成、查询、操作来自其他语言里内容的领域专用语言。避免注入攻击，如XSS、SQL注入等。

占位符 ${} 内则为JS表达式

```
let count = 10,
    price = 0.25,
    message = `${count} items cost $${(count * price).toFixed(2)}`;
```

## 函数&箭头函数

函数的默认参数值

```
function makeRequest(url, timeout = 2000, callback = function(){}){
    // do something
}
```

不定参数
可以指定对个各自独立的参数，通过整合后的数组进行访问
```
function pick(object, ...keys) {
    // do something
}
```
展开运算符
类似于不定参数，指定一个数组，将其打散后作为各自独立的参数传入函数。

```
let values = [12, 45, 43, 100];
Math.max.apply(Math, values);

Math.max(...values);

```

块级函数

```
"use strict"

if (true) {

    // ES5中报错，ES6中不报错
    function doSomeThing() {
        // ...
    }
}
```

箭头函数
1. 没有this、super、arguments、和new.target 绑定，值由外围最近一层的非箭头函数决定。
2. 不能通过new关键词调用，无[[construct]]方法，不能被作为构造函数。
3. 没有原型。
4. 不能改变this的绑定。
5. 不支持arguments。
6. 不支持从夫命名参数

```
let reflect = (value) => value;
let sum = (num1, num2) => num1 + num2;
```

业务中很多情况我们需要通过bind、闭包保存this等方法解决在处理回调函数时，考虑各种this的问题。

```
let PageHamdler = {
    id: '123456',
    
    init: function(){
        document.addEventListener('click', (function(event){
            this.doSomething(event.type);
        }).bind(this), false)
    },
    
    doSomething: function(type) {
        console.log(type + ' for ' + this.id);
    }
}

// 简洁形式

init: function(){
    document.addEventListener('click',
        event => this.doSomething(event.type), false)
}
```

> 需要注意在VUE不应该使用箭头函数来定义 method 函数 箭头函数绑定了父级作用域的上下文，所以 this 将不会按照期望指向 Vue 实例。



## 解构

打破数据结构，将其拆分为更小的部分的过程。

在嵌套过深的一些数据结构中，我们会进行频繁的判定，提取公共对象，取值等一系列操作。
而很多情况，我们可以使用 解构+默认值 的方式解决。

这是一坨动漫的业务老代码
```
const convertBanner = (obj,fromSelf,moduleTitle) => {
    let result = {};
    if (obj['page_id'] && obj['link_type'] == '1') {
        result.id = obj['page_id'];
        result.dataType = 'channel';
    }
    else if((obj['book_id'] && obj['link_type'] == '0') || !obj['link_type']){
        result.id = obj['book_id'];
        result.dataType = 'detail';
    }
    else if(obj['link_type'] == '2'){
        result.url = obj['url'];
        result.dataType = 'outerLink';	
    }
    result.title = obj['record_title'];
    result.pic = obj['pic'];
    result.cover = obj['pic'];
    result.dataFrom = fromSelf;
    result.moduleTitle = moduleTitle;    
    // todo server端的频道等其他参数。
    return result;
};


// 简单的优化（对于老代码的优化）
const convertBanner = (obj, dataFrom, moduleTitle) => {

    // 相关数据的说明
    let {
        url, link_type, pic, book_id
        page_id: id,
        record_title: title
    } = obj;

    let result = {
        pic,
        title,
        cover: pic,
        dataFrom,
        moduleTitle,
    };

    // 相关逻辑的说明
    if (link_type === '0' && book_id || !link_type ){
        result.id = book_id;
        result.dataType = 'detail';
    } else if (link_type === '1') {
        result.id = page_id;
        result.dataType = 'channel';
    } else if(link_type === '2'){
        result.url = url;
        result.dataType = 'outerLink';
    }

    return result;
};

// 如果是首次的编码
const convertBanner = (obj, dataFrom, moduleTitle) => {

    // 相关数据的说明
    let {
        url, link_type = '0', pic, book_id
        page_id: id,
        record_title: title
    } = obj;

    let result = {
        pic,
        title,
        cover: pic,

        dataFrom,
        moduleTitle,
    };

    // 相关逻辑的说明
    switch (link_type){
        case '0':
            result.id = book_id;
            result.dataType = 'detail';
            break;

        case '1':
            result.id = page_id;
            result.dataType = 'channel';
            break;

        case '2':
            result.url = url;
            result.dataType = 'outerLink';
            break;

        default:
            break;
    }

    return result;
};

```

## 类

## 其他点

迭代器&生成器

Promise与异步编程

对象，数组功能性的扩展

模块封装


