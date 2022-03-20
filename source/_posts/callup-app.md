---
title: 关于第三方应用中调起己方APP的Web实现方式
date: 2016-10-08
tags: 
- Intent
- JS
- Scheme
- Universal Links
- 移动端开发
---


在PC的Web时代，网站跳转问题仅仅简化为一个HTTP地址。到了移动时代，APP之间的相互调起就变得复杂起来，各类系统下应用间跳转的底层技术都不一样，调用方式等均不一致。没有任何协会致力于解决这个问题，进行规范化的处理。
### 基础调用（Intent/Scheme）
前端方面使用Intent协议链接或者Scheme协议链接的形式，通过web view的窗口进行吊起，当然部分APP中会禁止该方式，比如微信，QQ等。 目前使用 scheme 调起情况简单总结（简单检测，更随版本环境而定）：

> 安卓： 可正常直接调起：手百 搜狗 QQ 小米4自带浏览器 华为荣耀自带浏览器 需确认后可调起：UC，QQ浏览器，360浏览器 完全禁止：微信 chrome IOS： 可调起：手百，QQ浏览器，chrome，UC，360 （基本首次需确认是否调起） 完全禁止：微信 safari QQ (悲剧的是iOS10 对于外部scheme唤起全面禁用了 摔)

### 系统规范调用篇（App Links/Universal Links）
Android 和 iOS为了解决基础调用方式的复杂性依次推出了方便开发者得App Links技术。

*   谷歌叫做App Links(Android 6.0)
*   苹果叫做Universal Links（iOS9.0)

基本思想就是把打开应用的地址，统一为使用HTTP(S)方式，系统通过拦截和解析HOST地址，与系统注册的HOST进行匹配，如果发现就可以直接打开APP。 UniversalLinks 文档