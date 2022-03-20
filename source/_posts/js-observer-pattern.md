---
title: JS设计模式八：观察者模式
date: 2015-02-09
tags: 
- JS设计模式
- 设计模式
---

观察者模式对于前端开发来说也是一种接触较为频繁的软件设计模式，如果没注意过，那说明你还要好好学习啦。

观察者模式又称做发布订阅模式，基本定义为一对多或者多对多的依赖关系，由一个或者多个观察者对象来对一个或者多个发布者进行监听，由发布者的行为来触发各个观察者的行为。

观察者模式使的可以支持简单的广播通信，自动通知所有订阅过的对象，从而使得目标对象和各个观察者可以各自独立，减弱耦合度，使两者更容易扩展和重用。

简单的观察者模式也就无非为一个简单的对象，内部有一个存放函数的数组 list， add以及fire方法，add方法用来添加监听的回调函数进入lists数组，fire则是传递参数触发回调函数列表。这是JS中以回调函数形式实现观察者模式最简单的一种方式。

```
var Observable = {
    lists : [],
    add : function( fn ){
        this.lists.push( fn );
    },
    fire : function( args ){
        this.lists.forEach(fucntion(){
            fn( args );
        });
    }
}
```

结果当然是biubiu弹出两个console了。下面是一个稍微复杂点的观察者模式，包含了订阅，发布以及退订三个方法，基本可以实现一些简单的观察者模式。以围绕存储回调函数列表的对象 topics 进行订阅，发布和退订。

```
(function () {

    var q = window.pubsub = {};

    var topics = {},
        subUid = 0;

    // 发布
    q.publish = function (topic, args) {
        if (!topics[topic]) {
            return false;
        }
        var subscribers = topics[topic],
            len = subscribers ? subscribers.length : 0,
            i = 0;
        while (i < len) {
            subscribers[i].func(args);
            i++;
        }
        return true;
    };

    //订阅方法
    q.subscribe = function (topic, func) {
        if( !func ){
            return false;
        }
        if (!topics[topic]) {
            topics[topic] = [];
        }
        var token = subUid++;
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    };

    //退订方法
    q.unsubscribe = function (topic, token) {
        if (!topics[topic]) {
            return false;
        }
        var subscribers = topics[topic],
            len = subscribers ? subscribers.length : 0,
            i = 0;
        for (; i < len; i++) {
            if (subscribers[i].token === token) {
                subscribers.splice(i, 1);
                return token;
            }
        }
        return false;
    };
})();
```

一般来说这是JS中实现观察者模式最基本的形式，数组来存放回调，手动进行回调函数的添加，删除和触发。有点类似于一些库中的回调函数的形式。在复杂一点就是处理一下对于数据的储存方式等，下面是之前写过的一个自己的观察者模式实现方式：

subpub 模块，实现发布者与订阅者双向记录绑定 即：发布者中记录订阅该发布者的所有订阅者， 订阅者中记录该订阅者订阅的所有发布者。

实现创建 publisher 发布者 和 subscriber 订阅者 。

**publisher 实现**

1. publish 发布信息 
2. addSubscriber 添加订阅者 
3. deleteSubscriber 删除订阅者 
4. clear 清空订阅者。

**subscriber 实现** 

1. subscribe 订阅发布者 
2. unsubscribe 取消订阅发布者 
3. triggle 触发订阅者执行函数

```
(function( subpub ){
    if( typeof define === "function" && define.amd ){
        define( subpub );
    }else if( window.jQuery ){
        var old = jQuery.subpub,
            sp = subpub();
        jQuery.subpub = sp;
        jQuery.template.noConflict = function () {
            jQuery.subpub = old;
            return sp;
        }
    }else if( !window.subpub ){
        window.subpub = subpub();
    }else{
        throw new Error('Variable cookie is already exit in window');
    }
}(function(){
   
    // 由于发布者 订阅者是双向存储标记的 所以使用发布者ID和订阅者ID进行标记
    // 发布和订阅总对象来关联 ID 与 对象
    var PUBID = 0,
        pubObj = {},
        SUBID = 0,
        subObj = {};

    // 关于对象类型的均不采用鸭子辨别法 只用全等
    function has( array, item ){
        var value,i,len, hasOwn = {}.hasOwnProperty;
        if(array == null) return;

        if( array instanceof Array ){
            for(i=0, len=array.length; i<len; i++){
                if( array[i] === item ){
                    return true;
                }
            }
        }else{
            for(var key in array){
                if(hasOwn.call(array, key) && array[key] === item){
                    return true;
                }
            }
        }
        return false;
    }

    function each( array, iterator, context ){
        var value,i,len,
            forEach = [].forEach,
            hasOwn = {}.hasOwnProperty;

        context = context||this;

        if(array == null) return;

        if(forEach && array.forEach === forEach){
            array.forEach(iterator, context);
        }else if( array instanceof Array ){
            for(i=0, len=array.length; i<len; i++){
                iterator.call(context, array[i], i, array);
            }
        }else{
            for(var key in array){
                if(hasOwn.call(array, key)){
                    iterator.call(context, array[key], key, array);
                }
            }
        }
    }

    function publisher(){

        // 本来想将该属性私有封装，不过代码量会增加很多，并且也没有什么必要
        this.subIds = [];
        this.pubID = PUBID;
    }

    publisher.prototype = {

        // 发布 触发所有的 subIds id 列表成员的 trigger
        publish : function( info ){
            var subIds = this.subIds;

            each( subIds, function( subId ){
                subObj[subId].trigger( info );
            });

            return this;
        },

        // 发布者中添加 订阅者， 并在订阅者中记录发布者 单一订阅原则
        addSubscriber : function( subscriber ){
            var subIds = this.subIds,
                subId = subscriber.subID;

            // 单一订阅 也可以避免 订阅者 发布者中相互记录的无限循环
            if( has( subIds, subId ) ){
                return;
            }

            if(subscriber && subscriber.subscribe){
                subIds.push( subId );
                subscriber.subscribe( this );
            }else{
                throw new Error('subscriber is illegal');
            }
        },

        deleteSubscriber : function( subscriber ){
            var subIds = this.subIds,
                subId = subscriber.subID;

            if( !has( subIds, subId ) ){
                return;
            }

            for (var i = subIds.length - 1; i >= 0; i--) {
                if( subIds[i] === subId ){
                    subIds.splice(i, 1);
                }
            };

            subscriber.unsubscribe( this );

            return this;
        },

        clear : function(){
            var subIds = this.subIds;
            for (var i = subIds.length - 1; i >= 0; i--) {
                subObj[ subIds[i] ].unsubscribe( this );
            };

            this.subIds = [];
        }
    }


    function subscriber( func ){
        this.pubIds = [];
        this.subID = SUBID;
        this.callbck = func;
    }

    subscriber.prototype = {
        subscribe : function( publisher ){
            var pubIds = this.pubIds,
                pubId = publisher.pubID;

            if( has( pubIds, pubId ) ){
                return;
            }

            if( publisher && publisher.addSubscriber ){
                pubIds.push( pubId );
                publisher.addSubscriber(this);
            }else{
                throw new Error('publisher is illegal');
            }
        },

        unsubscribe : function( publisher ){
            var pubIds = this.pubIds,
                pubId = publisher.pubID;

            if( !has( pubIds, pubId ) ){
                return;
            }

            for (var i = pubIds.length - 1; i >= 0; i--) {
                if( pubIds[i] === pubId ){
                    pubIds.splice(i, 1);
                }
            };

            publisher.deleteSubscriber( this );

            return this;
        },

        clear : function(){
            var pubIds = this.pubIds;
            for (var i = pubIds.length - 1; i >= 0; i--) {
                pubObj[ pubIds[i] ].deleteSubscriber( this );
            };

            this.pubIds = [];
        },

        trigger : function( args ){
            this.callbck( args );
        }
    }

    return {
        creatPublisher : function(){
            var pub = new publisher();
            pubObj[ PUBID++ ] = pub;

            return pub;
        },
        creatSubscriber : function( func ){
            var sub = new subscriber( func );
            subObj[ SUBID++ ] = sub;

            return sub;
        }
    }
}));

    实例：

var sp = window.subpub;

var a1 = sp.creatPublisher(), a2 = sp.creatPublisher(), a3 = sp.creatPublisher();
var b1 = sp.creatSubscriber(function(a){ console.log('b1 '+a) }),
    b2 = sp.creatSubscriber(function(a){ console.log('b2 '+a) }),
    b3 = sp.creatSubscriber(function(a){ console.log('b3 '+a) });

a1.addSubscriber(b1)
a1.addSubscriber(b3)
a2.addSubscriber(b2)
a3.addSubscriber(b1)
a3.addSubscriber(b2)
a3.addSubscriber(b3)


< a1.publish("this is a1 publish")
> "b1 this is a1 publish"
> "b3 this is a1 publish"

< a3.deleteSubscriber(b1)
< a3.deleteSubscriber(b2)
< a3.publish("this is a3 publish")
> "b3this is a3 publish"

< b3.unsubscribe(a3)
< b2.subscribe(a3)
< a3.publish("this is a3 publish")
> "b2 this is a3 publish"
```

简单学习了那么些设计模式之后，其实越来越觉得设计模式开始越来越抽象化，成为一种类似于现实如何转化为逻辑，思维的一种方式。所以个人认为，如何将事物抽象化，各种方式主要解决的针对性问题是什么，或许就是设计模式存在的真正意义吧。

不足之处，望读者指出，谢谢~