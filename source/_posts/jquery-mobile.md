---
title: jQuery Mobile介绍
date: 2013-05-09
tags: 
- jQuery Mobile
---

### 什么是jQuery Mobile？
最初在没有接触 jQuery Mobile 之前，以为 jQuery Mobile 是独立的应用于移动端设备的 JS 库。但准确的来讲，jQuery Mobile是是一个依赖于 jQuery， 应用于手机和平板设备上的库。jQuery Mobile 不仅会给主流移动平台带来jQuery核心库，而且还包含一个完整统一的jQuery移动的UI框架。解决移动端样式兼容性问题，支持全球主流的移动平台。 下面是jQuery Mobile 兼容的移动操作系统。 ![](http://image.freefe.cc/26672038_1368002755QVL2.png) 以上8个主流系统应该涵盖了当前 90% 以上的移动端产品。Jquery mobile构建于Jquery ，为前端开发人员提供了一个兼容所有主流移动设备平台的统一UI接口系统。拥有出色的弹性，轻量化以及渐进增强特性与可访问性。 Jquery Mobile的策略可以很容易的概括：创建一个顶级的javascript库，在不同的智能手机和桌面电脑的web浏览器上,形成统一的用户ui. 目前Jquery Mobile的最新稳定版本为已经为正式版1.3,在2013年2月上旬发布，jQuery Mobile 1.3支持jQuery1.7.2或 1.9.1版本。
### jQuery Mobile的特性

*   构建于jQuery的核心之上。使之兼容于jq的语法，对于开发人员有最易的开发曲线
*   兼容于所有的主流移动手机，平板电脑，电子书,和pc，iOS, Android, Blackberry, Palm WebOS, Nokia/Symbian, Windows Phone 7, MeeGo, Opera Mobile/Mini, Firefox Mobile, Kindle, Nook, 和所有的现代浏览器。
*   轻量级 压缩后只12k，对图片的依赖程度非常低，保证了速度
*   模块化的架构可以根据你的独特的需求用来构建最优化的应用
*   页面和行为均基于html5标记的驱动进行配，开发效率高，对脚本的需求小
*   渐进增强使所有的移动设备，平板电脑和pc电脑都支持核心的内容和方法。而对于新的移动平台，则可以展现像安装在设备中的应用程序一样出色的富媒体和交互的浏览体验
*   弹性的设计可以使同样的代码在智能设备上和桌面的屏幕上都自动缩放适应。
*   强大的ajax驱动的导航系统在保持后退按钮，收藏夹和干净的地址栏的同时完成页面转场。
*   优秀的可访问性 一些特性比如WAI-ARIA 也包含在内，以确保页面也可以在一些屏幕阅读器(比如苹果的VoiceOver)或者其他手持设备中正常工作.
*   支持触摸和鼠标事件增加了触摸屏设备支持的触摸，鼠标，和基于光标的输入方法的API
*   统一的UI组件增加了触摸屏设备支持的触摸，鼠标，和基于光标的输入方法的API
*   强大的主题样式框和主题编辑器能很容易的进行高度个性化和品牌化的的界面定制

### jQuery Mobile的不足
下面也是查询网上，以及自己感觉的对于 jQuery Mobile 的不足。

*   首先 jQuery Mobile 对于开发的简便性，兼容性 导致了需要对页面结构进行复杂化，ui组件对于元素 class 的泛滥。
*   在性能上的一些不足，会导致在一些移动端处很卡的现象。
*   jQuery Mobile 需要对页面进行从新的构建，所以对于一些异步加载的数据就会有一些问题。

### 代码示例 JM 重构HTML
下面是一段使用 JM 的HTML代码，看看 JM 对其做的重构

*   Components
*   Pages & dialogs
*   Toolbars
*   Buttons
*   Content formatting
*   Form elements
*   Listviews

其中的 data-role，data-inset，data-theme等都是所谓的 HTML5 标记，JM是根据这些标记进行对页面的配置的。具体的每一个标记都是相当于 JM 中的一个控制器是。（标识数据属性） 然后结果 JM ui的从新构建后 会变成

*   Components
*   Pages & dialogs

...

可以看到，原先的只是一个包含 a 标签的 li 元素被重新构建成了一个非常复杂的结构。并且自动添加很多 JM ui 的样式。 当然，最后的ui效果是很不错的。 ![](http://image.freefe.cc/26672038_1368071567rLzo.png) 当然这部分是需要后端渲染出来，如果是前端加载，AJAX的方式请求数据渲染的话，那么模板则需要使用 JM ui 重新构建之后的 HTML 格式了，并且还需要进行一些处理。（可能还有其他较为好的方法）。 总的来说，对于一些需要快速建立简单移动站，又希望兼容性比较高的WEB开发工程师来说，jquery mobile是非常好的选着。更多学习 JM 可以参考 http://www.jqmapi.com/ .