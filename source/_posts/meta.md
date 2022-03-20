---
title: meta标签能解决的事情
date: 2016-10-25
tags: 
- HTML
- meta
- seo
---

> 标签提供关于HTML文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。它可用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他 web 服务。 —— `W3School`

`必选属性` ![](http://image.freefe.cc/20161025203116.png) `可选属性` ![](http://image.freefe.cc/20161025203140.png)

### SEO优化

_页面关键词_：每个网页应具有描述该网页内容的一组唯一的关键字。

```
<meta name="keywords" content="站点关键词" />
```

_页面描述_：每个网页都应有一个不超过 150 个字符且能准确反映网页内容的描述标签。

```
<meta name="description" content="站点描述" />
```

_搜索引擎索引方式_：robotterms是一组使用逗号(,)分割的值，通常有如下几种取值：none，noindex，nofollow，all，index和follow

```
<meta name="robots" content="index,follow" />
<!--
all：文件将被检索，且页面上的链接可以被查询；
none：文件将不被检索，且页面上的链接不可以被查询；
index：文件将被检索；
follow：页面上的链接可以被查询；
noindex：文件将不被检索；
nofollow：页面上的链接不可以被查询。
 -->
```

_页面重定向和刷新_ content内的数字代表时间（秒），既多少时间后刷新

```
<meta http-equiv="refresh" content="0;url=http://www.baidu.com" />
```

### 移动设备

_viewport_：能优化移动浏览器的显示。如果不是响应式网站，不要使用initial-scale或者禁用缩放。

```
<meta name="viewport" content="width=device-width, initial-scale=0,maximum-scale=0, user-scalable=no"/>
```

*   width：宽度（数值 / device-width）（范围从200 到10,000，默认为980 像素）
*   height：高度（数值 / device-height）（范围从223 到10,000）
*   initial-scale：初始的缩放比例 （范围从>0 到10）
*   minimum-scale：允许用户缩放到的最小比例
*   maximum-scale：允许用户缩放到的最大比例
*   user-scalable：用户是否可以手动缩 (no,yes)
*   minimal-ui：可以在页面加载时最小化上下状态栏。（已弃用）

_忽略数字自动识别为电话号码_

```
<meta content="telephone=no" name="format-detection" />
```

_忽略识别邮箱_

```
<meta content="email=no" name="format-detection" />
```

_添加智能 App 广告条 Smart App Banner_：告诉浏览器这个网站对应的app，并在页面上显示下载banner(如下图)。文档链接

```
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">


<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized" content="320">
<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">
```

### 网页相关

_申明编码_

```
<meta charset='utf-8' />
```

_优先使用 IE 最新版本和 Chrome_

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<!-- 关于X-UA-Compatible -->
<meta http-equiv="X-UA-Compatible" content="IE=6" ><!-- 使用IE6 -->
<meta http-equiv="X-UA-Compatible" content="IE=7" ><!-- 使用IE7 -->
<meta http-equiv="X-UA-Compatible" content="IE=8" ><!-- 使用IE8 -->
```

_浏览器内核控制_：国内浏览器很多都是双内核（webkit和Trident），webkit内核高速浏览，IE内核兼容网页和旧版网站。而添加meta标签的网站可以控制浏览器选择何种内核渲染。

```
<meta name="renderer" content="webkit|ie-comp|ie-stand">
```

_禁止浏览器从本地计算机的缓存中访问页面内容_：这样设定，访问者将无法脱机浏览。

```
<meta http-equiv="Pragma" content="no-cache">
```

_转码申明_：用百度打开网页可能会对其进行转码（比如贴广告），避免转码可添加如下meta

```
<meta http-equiv="Cache-Control" content="no-siteapp" />
```