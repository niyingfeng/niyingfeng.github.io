---
title: 移动端点击事件延迟问题
date: 2016-09-23
tags: 移动端点击时间延迟
---

iOS和Android系统上的webkit内核的浏览器以及嵌入在应用程序里面的webview中，两次连续“轻触”判断为“放大”的操作，所以在第一次被“轻触”后，浏览器仍需要等待判定是否具有第二次“轻触”操作，从而来决定是触发“放大”操作还是触发单击click事件，直接导致click时间具有300ms延迟的问题。

### 方案一

使用touchstart事件，该dom上触摸开始触发事件。

### 方案二

模拟快速点击方式，首先触发touchstart，并且在未触发touchmove的情况下，短时间内触发touchend，即算作快速点击事件，进行触发。如 zepto内的 tap事件，以及 fastclick

> 备注：touch事件会影响click事件，若touchend添加阻止默认事件，click事件则不会触发。 touchstart → touchmove → touchend touchstart → touchend → click