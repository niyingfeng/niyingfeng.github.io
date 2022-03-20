---
title: FastClick源码分析
date: 2017-01-19
tags: 
- FastClick源码
- FastClick
---
**问题1:** 大部分iOS浏览器中 click 时间有延迟感，没有及时触发绑定事件。

**问题2：**使用touchend，或者zepto的tap事件，出现一些奇观的穿透点击触发事件

**主要原因：**iOS历史上一直存在页面的默认事件-双击放大，导致对于浏览器判断此次是否为双击时间需要设置一定的等待时间，故而click点击事件相对于touchend时间会延迟300ms左右，以保证此次确定为单次的点击事件。

**FastClick为用来解决消除移动浏览器上的点击事件与触摸事件之间的300ms。以下为其简单的代码逻辑。**

1.首先，在初始化时 对touch click各类事件各绑定一个监听函数 保证触发时在同个事件中为最早触发的

```
function FastClick(layer, options) {
    var oldOnClick;
    options = options || {};

    this.trackingClick = false; // 当前的点击是否是被标记的
    this.trackingClickStart = 0; // 点击被标记时的时间戳
    this.targetElement = null; // 被标记点击的元素
    
    this.touchStartX = 0; // touch开始时的横坐标值
    this.touchStartY = 0; //  touch开始时的纵坐标值
    this.lastTouchIdentifier = 0; // 最后一次touch的id, 为Touch.identifier.
    this.touchBoundary = options.touchBoundary || 10; // 标识 Touchmove 的分界线, 如超过后则取消触发点击.

    this.layer = layer; // 绑定FastClick的layer.
    this.tapDelay = options.tapDelay || 200; // 判断为 tap(touchstart 和 touchend) 事件之间最小时间间隔
    this.tapTimeout = options.tapTimeout || 700; // 判断为 tap 的最大时间间隔

    if (FastClick.notNeeded(layer)) {
        return;
    }

    // 部分老版本安卓没有 Function.prototype.bind
    function bind(method, context) {
        return function() { return method.apply(context, arguments); };
    }

    // 添加实例的几个事件方法
    var methods = ['onMouse', 'onClick', 'onTouchStart', 'onTouchMove', 'onTouchEnd', 'onTouchCancel'];
    var context = this;
    for (var i = 0, l = methods.length; i < l; i++) {
        context[methods[i]] = bind(context[methods[i]], context);
    }

    // 更具需要来设置事件
    if (deviceIsAndroid) {
        layer.addEventListener('mouseover', this.onMouse, true);
        layer.addEventListener('mousedown', this.onMouse, true);
        layer.addEventListener('mouseup', this.onMouse, true);
    }

    layer.addEventListener('click', this.onClick, true);
    layer.addEventListener('touchstart', this.onTouchStart, false);
    layer.addEventListener('touchmove', this.onTouchMove, false);
    layer.addEventListener('touchend', this.onTouchEnd, false);
    layer.addEventListener('touchcancel', this.onTouchCancel, false);

    // 对于浏览器不支持事件 stopImmediatePropagation的hack一下 (e.g. Android 2)
    // 这是 FastClick 通常如何阻止的形式 点击事件在冒泡到 FastClick layer 注册的回调方法之前就被取消。

    if (!Event.prototype.stopImmediatePropagation) {
        layer.removeEventListener = function(type, callback, capture) {
            var rmv = Node.prototype.removeEventListener;
            if (type === 'click') {
                rmv.call(layer, type, callback.hijacked || callback, capture);
            } else {
                rmv.call(layer, type, callback, capture);
            }
        };

        // 重写layer的绑定事件方法 单独处理click
        layer.addEventListener = function(type, callback, capture) {
            var adv = Node.prototype.addEventListener;
            if (type === 'click') {
                adv.call(layer, type, callback.hijacked || (callback.hijacked = function(event) {
                    if (!event.propagationStopped) {
                        callback(event);
                    }
                }), capture);
            } else {
                adv.call(layer, type, callback, capture);
            }
        };
    }

    // 如果事件句柄已经被绑定在元素的 onclick 属性上, 会在 FastClick的onClick事件之前触发
    // 通过创建用户定义事件句柄函数，将其添加到监听中来解决
    if (typeof layer.onclick === 'function') {

        // 低于3.2的Android浏览器需要将 layer.onclick 指向新的引用
        // 如果直接传递，会有有一定问题
        oldOnClick = layer.onclick;
        layer.addEventListener('click', function(event) {
            oldOnClick(event);
        }, false);
        layer.onclick = null;
    }
}
```


2.在点击过程中，会在 touchstart 初始化记录一些数据，用于之后的逻辑判断处理


```
FastClick.prototype.onTouchStart = function(event) {
    var targetElement, touch, selection;

    // 忽略多点 touch, 否则 在FastClick元素上的双指触摸放大缩小会被阻止 (issue #111).
    if (event.targetTouches.length > 1) {
        return true;
    }

    targetElement = this.getTargetElementFromEventTarget(event.target);
    touch = event.targetTouches[0];

    if (deviceIsIOS) {

        // 在iOS中 只有通过的事件才会触发文案的选择取消 (issue #49)
        selection = window.getSelection();
        if (selection.rangeCount && !selection.isCollapsed) {
            return true;
        }

        if (!deviceIsIOS4) {

            // iOS下奇怪的事件是当 alert 或者 confirm 对话框是被点击事件的回调方法打开的(issue #23):
            if (touch.identifier && touch.identifier === this.lastTouchIdentifier) {
                event.preventDefault();
                return false;
            }

            this.lastTouchIdentifier = touch.identifier;

            // 如果目标元素是一个滚动元素的子元素时 (使用了 -webkit-overflow-scrolling: touch) 并且:
            // 1) 用户在滚动的layer上滚动
            // 2) 用户使用tap来停止滚动
            // 那么 event.target 的最后的 touchend 事件将是用户最后手指下面的元素的事件
            // 当滚动开始时, 会触发 FastClick 将点击事件传递到那个layer - unless a check
            // 除非在发送合成点击事件前检测其父级layer并非是滚动的 (issue #42).
            this.updateScrollParent(targetElement);
        }
    }
    
    // 为重要标识 标识此次的触发行为为指定的fastclick
    this.trackingClick = true;
    this.trackingClickStart = event.timeStamp;
    this.targetElement = targetElement;

    this.touchStartX = touch.pageX;
    this.touchStartY = touch.pageY;

    // 防止双击触发fast click (issue #36)
    if ((event.timeStamp - this.lastClickTime) < this.tapDelay) {
        event.preventDefault();
    }

    return true;
};
```

3.若有触发 touchmove 则使用自定义的函数进行判断，并且确认是否此次touch 不作为click 处理

```
FastClick.prototype.onTouchMove = function(event) {
    if (!this.trackingClick) {
        return true;
    }

    // 如果此次 touch 算作移动 , 取消该点击的跟踪
    if (this.targetElement !== this.getTargetElementFromEventTarget(event.target) || this.touchHasMoved(event)) {
        this.trackingClick = false;
        this.targetElement = null;
    }

    return true;
};
```

4.touchend 时，判断此次时间是否符合click条件 合成click事件来进行模拟触发保证绑定在该元素上的点击事件能在touchend时刻立即触发， 阻止事件冒泡以及去掉默认事件使得浏览器自身的延迟的点击事件不在触发。

```
FastClick.prototype.onTouchEnd = function(event) {
    var forElement, trackingClickStart, targetTagName, scrollParent, touch, targetElement = this.targetElement;

    if (!this.trackingClick) {
        return true;
    }

    // 阻止构造的快速双击 (issue #36)
    if ((event.timeStamp - this.lastClickTime) < this.tapDelay) {
        this.cancelNextClick = true;
        return true;
    }

    if ((event.timeStamp - this.trackingClickStart) > this.tapTimeout) {
        return true;
    }

    // 重置错误的input上的点击取消 (issue #156).
    this.cancelNextClick = false;

    this.lastClickTime = event.timeStamp;

    trackingClickStart = this.trackingClickStart;
    this.trackingClick = false;
    this.trackingClickStart = 0;

    // 在一些iOS设备中, 如果目标元素的层正处于过渡变换或者滚动的时候，
    // 其对于事件的支持是无效的除非再次手动检测. 
    // See issue #57; also filed as rdar://13048589 .
    if (deviceIsIOSWithBadTarget) {
        touch = event.changedTouches[0];

        // 在某些情况下 elementFromPoint 未负数, 所以阻止将目标元素设置为null
        targetElement = document.elementFromPoint(touch.pageX - window.pageXOffset, touch.pageY - window.pageYOffset) || targetElement;
        targetElement.fastClickScrollParent = this.targetElement.fastClickScrollParent;
    }

    // 为label标签的时候 findControl 就近查找元素
    targetTagName = targetElement.tagName.toLowerCase();
    if (targetTagName === 'label') {
        forElement = this.findControl(targetElement);
        if (forElement) {
            this.focus(targetElement);
            if (deviceIsAndroid) {
                return false;
            }

            targetElement = forElement;
        }
    } else if (this.needsFocus(targetElement)) {
        // 需要聚焦后模拟点击的
        
        // Case 1 快速双击情况: touch触发在一会会之前 (基于测试 100ms 左右 for issue #36) 出发聚焦.
        // 尽早返回并且不设置target element的引用 以便后续的点击可被通过.
        // Case 2: 当document处于一个iframe内部时，所有的输入元素都如此 (issue #37).
        if ((event.timeStamp - trackingClickStart) > 100 || (deviceIsIOS && window.top !== window && targetTagName === 'input')) {
            this.targetElement = null;
            return false;
        }

        this.focus(targetElement);
        this.sendClick(targetElement, event);

        // iOS 4 选择元素直接pass
        if (!deviceIsIOS || targetTagName !== 'select') {
            this.targetElement = null;
            event.preventDefault();
        }

        return false;
    }

    if (deviceIsIOS && !deviceIsIOS4) {

        // 父级为滚动layer的情况(usually initiated by a fling - issue #42).
        scrollParent = targetElement.fastClickScrollParent;
        if (scrollParent && scrollParent.fastClickLastScrollTop !== scrollParent.scrollTop) {
            return true;
        }
    }

    // 阻止真实点击通过 - 除非是标记的元素
    // real clicks or if it is in the whitelist in which case only non-programmatic clicks are permitted.
    if (!this.needsClick(targetElement)) {
        event.preventDefault();
        this.sendClick(targetElement, event);
    }

    return false;
};

FastClick.prototype.sendClick = function(targetElement, event) {
    var clickEvent, touch;

    // 在一些安卓设备下，需要先进行聚焦，否则无法调用合成的click事件 (#24)
    if (document.activeElement && document.activeElement !== targetElement) {
        document.activeElement.blur();
    }

    touch = event.changedTouches[0];

    // 合成一个点击事件, 添加一个额外属性以便于跟踪
    // 合成事件 还真没见过
    clickEvent = document.createEvent('MouseEvents');
    clickEvent.initMouseEvent(this.determineEventType(targetElement), true, true, window, 1, touch.screenX, touch.screenY, touch.clientX, touch.clientY, false, false, false, false, 0, null);
    clickEvent.forwardedTouchEvent = true;
    targetElement.dispatchEvent(clickEvent);
};
```

> 总结：FastClick 经典之处在于，初始化时绑定第一时间触发的touch事件，在touchend时便判断此次行为是否可作为click，如果此次touch满足click事件，则在touchend事件中直接合成click并且进行触发，然后再touchend中阻止事件冒泡和默认事件等，来防止默认延迟的click事件延迟后又被触发。