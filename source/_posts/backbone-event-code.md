---
title: BackBone事件源码
date: 2013-05-31
tags: 
- backbone
- backbone事件源码
- JS
---

### backbone 简介
当我们在开发大量 JavaScript 的 web 应用的时候，很大的工作就是处理 DOM 以及 DOM 中数据的问题。如果没有很好的组装架构，那么就会带来混乱的结构与越来越复杂和麻烦的问题。 backbone 是一个重量级的 JavaScript 应用框架。 为复杂的 JavaScript 应用程序提供了模型（models）、集合（collections）、视图（views）的结构。

*   模型：用于绑定键值数据和自定义事件
*   集合：附加有可枚举函数的丰富API
*   视图：声明事件处理函数，通过RESRful JSON接口连接到应用程序。

backbone.js 对于 Underscore.js 是重度依赖，对于RESTful，history则依赖与Backbone.Router，DOM处理依赖于 Backbone.View，json2.js，Jquery或者Zepto。 Backbone 将数据呈现为模型，可以创建模型、验证模型、销毁模型和保存模型至服务器。当 UI 的改变引起模型属性变化的时候就会触发 "change" 事件，所显示模型数据的 视图 会接受到更改通知，重新渲染。
### backbone 使用
`backbone.Events` backbone 中的 event 模块可以被拓展到任意对象，使任何对象都可以使用自定义事件。

```
```
var Events = Backbone.Events = {
// 对一个事件绑定回调函数. 传递 "all" 则将绑定回调在所有事件触发时调用
on: function(name, callback, context) {
  // API 检测 
  if (!eventsApi(this, 'on', name, [callback, context]) || !callback) return this;
  //相当于 this._events = this._events || {} 下面语法更容易用来赋值 如下下句
  //在绑定时间的对象中 建立事件池 来进行事件管理
  this._events || (this._events = {}); 
  // name 事件在事件池中的形式（数组形式） 存放当前对象绑定在name的所有事件
  var events = this._events[name] || (this._events[name] = []);
  // 将当前需要绑定的事件 push到事件池中的具体事件名称中
  events.push({callback: callback, context: context, ctx: context || this});
  return this;
},
off: function(){},
once: function(){},
trigger: function(){},
stopListening: function(){}
}
```
```

其实以上方法和 Jquery 的实现类似，只不过更为简单，Jquery 中还对当前需要绑定事件的对象如果为 DOM 元素的话需要进一步处理来保证 IE 中内存遇到的问题。

```
```
// 解绑一个或者多个回调.
// 如果 `context` 是 null, 移除所有有该函数的回调.
// 如果 `callback` 是 null, 移除该事件下的所有回调. 
// 如果 `name` 是 null, 移除所有帮定的回调函数
off: function(name, callback, context) {
    var retain, ev, events, names, i, l, j, k;
    if (!this._events || !eventsApi(this, 'off', name, [callback, context])) return this;

    // obj.off() 移除所有事件
    if (!name && !callback && !context) {
        this._events = {};
        return this;
    }

    // 使用 underscore 的 获取对象键值方法
    names = name ? [name] : _.keys(this._events);
    for (i = 0, l = names.length; i < l; i++) {
        name = names[i];
        // 移除某一事件下的回调
        if (events = this._events[name]) {
            this._events[name] = retain = [];
            if (callback || context) {
                for (j = 0, k = events.length; j < k; j++) {
                    ev = events[j];
                    if ((callback && callback !== ev.callback && callback !== ev.callback._callback) ||
                            (context && context !== ev.context)) {
                        // 不是所需要删除的事件 则重新扔回 事件池中的对应名称中
                        retain.push(ev);
                    }
                }
            }
            //如果对应的名称中没有事件，那么删除事件池对象的该属性
            if (!retain.length) delete this._events[name];
        }
    }
    return this;
}
```
```

事件移除，简单地说因为事件绑定是使用了 事件池 的方法处理，那么对于移除事件只需要在事件池中 delete 掉响应的事件即可，唯一需要做的是兼容参数个数不同情况下的移除事件情况。

```
```
// 触发一个或多个事件, 触发所有绑定的回调.
// 除了事件名称，回调函数会被传递'trigger'相同的参数。 
// (如果你监听了 'all', 会让你的回调函数将事件名称作为第一个参数).
// obj.trigger("change",function(){});
// obj.trigger("all",function(eventName){ alert(eventName) });
trigger: function(name) {
    if (!this._events) return this;
    var args = slice.call(arguments, 1);
    if (!eventsApi(this, 'trigger', name, args)) return this;
    var events = this._events[name];
    //别忘了任何事件触发都要调用 all 中的事件
    var allEvents = this._events.all;
    if (events) triggerEvents(events, args);
    if (allEvents) triggerEvents(allEvents, arguments);
    return this;
},

// 使这个对象或者所有监听特定事件的对象停止监听该特定的事件
stopListening: function(obj, name, callback) {
    var listeners = this._listeners;
    if (!listeners) return this;
    var deleteListener = !name && !callback;
    if (typeof name === 'object') callback = this;
    if (obj) (listeners = {})[obj._listenerId] = obj;
    for (var id in listeners) {
        listeners[id].off(name, callback, this);
        if (deleteListener) delete this._listeners[id];
    }
    return this;
}
```
```

触发与停止监听。

```
```
// 分割事件字符串.
var eventSplitter = /s+/;
// 实现多样式化的事件功能的API 
//比如现有API中的多名称的change blur和jquery风格的事件映射 {change: action}
var eventsApi = function(obj, action, name, rest) {
    if (!name) return true;
    // 处理事件映射
    if (typeof name === 'object') {
        for (var key in name) {
            obj[action].apply(obj, [key, name[key]].concat(rest));
        }
        return false;
    }
    // 处理空间分割的事件名称.
    if (eventSplitter.test(name)) {
        var names = name.split(eventSplitter);
        for (var i = 0, l = names.length; i < l; i++) {
            obj[action].apply(obj, [names[i]].concat(rest));
        }
        return false;
    }
    return true;
};
// A difficult-to-believe, 在触发事件时优化内部调用. 尽可能快速到达最有可能的情况
// (核心的3个 Backbone 事件参数).
var triggerEvents = function(events, args) {
    var ev, i = -1, l = events.length, a1 = args[0], a2 = args[1], a3 = args[2];
    switch (args.length) {
        case 0: while (++i < l) (ev = events[i]).callback.call(ev.ctx); return;
        case 1: while (++i < l) (ev = events[i]).callback.call(ev.ctx, a1); return;
        case 2: while (++i < l) (ev = events[i]).callback.call(ev.ctx, a1, a2); return;
        case 3: while (++i < l) (ev = events[i]).callback.call(ev.ctx, a1, a2, a3); return;
        default: while (++i < l) (ev = events[i]).callback.apply(ev.ctx, args);
    }
};
var listenMethods = {listenTo: 'on', listenToOnce: 'once'};
// 使用listenTo 和 listenToOnce反转 on and once 控制. 将 *this* 对象监听另外一个对象中的事件
// 保持对监听的跟踪
_.each(listenMethods, function(implementation, method) {
    Events[method] = function(obj, name, callback) {
        var listeners = this._listeners || (this._listeners = {});
        var id = obj._listenerId || (obj._listenerId = _.uniqueId('l'));
        listeners[id] = obj;
        if (typeof name === 'object') callback = this;
        obj[implementation](name, callback, this);
        return this;
    };
});
// 向后兼容的名称.
Events.bind = Events.on;
Events.unbind = Events.off;
```
```

使用的实例为

```
```
var object = {};
_.extend(object, Backbone.Events);
object.bind("alert", function(msg) {
    alert("Triggered " + msg);
});
object.trigger("alert", "www.csser.com");


object.unbind("change", onChange); // 只移除onChange回调函数
object.unbind("change"); // 移除所有 "change" 回调函数
object.unbind();
```
```

`backbone.sync` _backbone.sync( method, model, [option] )_

*   method - CRUD 方法 ("create", "read", "update", 或 "delete")
*   model - 要被保存的模型（或要被读取的集合）
*   options - 成功和失败的回调函数，以及所有 jQuery 请求支持的选项

是对于模型在服务器中读取和保存的函数,都为 json 格式进行数据传输。默认情况下是依赖于 jQuery或者Zepto 的 Ajax 方法进行的发送 RESTful json 请求。（关于 REST Representational State Transfer 表现层状态转化 可见阮一峰老师的 理解RESTful架构）。额~反正本人不是很了解。

```
```
// Backbone.sync 同步
// -------------
// 重载这个方法来改变 Backbone 持久模型在服务器中的方式、
// 通过请求类型和问题中的模型
// 默认情况发送 RESTful Ajax 请求到模型中的`url()`.
// 一些可行的自定义为:
//
// * 使用 `setTimeout` 在一个请求中批量的触发更新.
// * 发送 XML 而不是 JSON.
// * 坚持模型通过 WebSockets 而不是 Ajax.
//
// 开启 `Backbone.emulateHTTP` 为像 `POST` 一样发送 `PUT` 和 `DELETE` 请求
// `_method` 参数中包含真正的 HTTP 方法,
// 以及主体的所有请求 as `application/x-www-form-urlencoded`
// 替换为 `application/json`参数名为 `model`的模块.
// 当接口为服务器端语言，如**PHP**时很有用， 使得主体的'PUT'请求难以读取
Backbone.sync = function(method, model, options) {
    var type = methodMap[method];

    // 默认设置, 除非指定.
    _.defaults(options || (options = {}), {
        emulateHTTP: Backbone.emulateHTTP,
        emulateJSON: Backbone.emulateJSON
    });

    // 默认 JSON-request 设置.
    var params = {type: type, dataType: 'json'};

    // 确保有一个 URL.
    if (!options.url) {
        params.url = _.result(model, 'url') || urlError();
    }

    // 确保我们有正确的请求数据.
    if (options.data == null && model && (method === 'create' || method === 'update' || method === 'patch')) {
        params.contentType = 'application/json';
        params.data = JSON.stringify(options.attrs || model.toJSON(options));
    }

    // 对于老的服务器, 模拟JSON以HTML形式的.
    if (options.emulateJSON) {
        params.contentType = 'application/x-www-form-urlencoded';
        params.data = params.data ? {model: params.data} : {};
    }

    // 对于老的服务器, 模拟 HTTP 通过用 `_method` 方法仿造 HTTP 
    // 和一个 `X-HTTP-Method-Override` 头.
    if (options.emulateHTTP && (type === 'PUT' || type === 'DELETE' || type === 'PATCH')) {
        params.type = 'POST';
        if (options.emulateJSON) params.data._method = type;
        var beforeSend = options.beforeSend;
        options.beforeSend = function(xhr) {
            xhr.setRequestHeader('X-HTTP-Method-Override', type);
            if (beforeSend) return beforeSend.apply(this, arguments);
        };
    }

    // 在 non-GET 请求中不传递数据.
    if (params.type !== 'GET' && !options.emulateJSON) {
        params.processData = false;
    }

    // 如果我们发送 `PATCH` 请求, 
    // 我们在一个老版本ActiveX默认启动的情况下，使用XHR来取代jQuery方法。
    // 删除它当IE8支持 `PATCH` 的时候.
    if (params.type === 'PATCH' && window.ActiveXObject &&
                !(window.external && window.external.msActiveXFilteringEnabled)) {
        params.xhr = function() {
            return new ActiveXObject("Microsoft.XMLHTTP");
        };
    }

    //提出请求, 允许用户自定义Ajax选项.
    var xhr = options.xhr = Backbone.ajax(_.extend(params, options));
    model.trigger('request', model, xhr, options);
    return xhr;
};

// 映射 CRUD 到 HTTP 为了默认的 `Backbone.sync` 执行.
var methodMap = {
    'create': 'POST',
    'update': 'PUT',
    'patch': 'PATCH',
    'delete': 'DELETE',
    'read': 'GET'
};

// 通过 `$` 代理来设置 `Backbone.ajax` 的默认执行.
// 如果想要使用另一个库那么重载它.
Backbone.ajax = function() {
    return Backbone.$.ajax.apply(Backbone.$, arguments);
};
```
```

### backbone 源码注释
以上为 bockbone 的2个核心的模块，另外模块将在下两篇中简单介绍。 由于源码过长，请 查看 Github 猛击这里 。这里只为对 backbone 原文注释的简单翻译，具体还需要研究。 对于 backbone 的中文文档，请 点击这里 这是一个不错的中文文档，各个接口均有详细说明。