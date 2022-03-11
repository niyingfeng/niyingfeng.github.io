---
title: 移动端通用性能点
date: 2017-01-10
tags: web性能,移动端开发
---
## 移动性能

简单总结对于移动端上，前端开发中可以注意的性能点。各点都有利有弊，仍需按照各自情况权衡考虑。

## 加载时性能优化考虑

### 图片

*   图片压缩，适度考虑有损压缩，包括考虑WebP图片格式
*   CSS Sprites 或者 base64编码代替图片（文件变大，还需解码），须注意使用场景以及图片大小
*   使用CSS3来绘制简单的图形与图像
*   图片资源依照可视区域进行按需加载
*   小型大批量的小ICON 考虑使用字体形式展现处理
*   使用 srcset 屏幕密度现实对应尺寸图片

### 资源

*   CSS 中避免 @import 引入
*   确保静态资源优化压缩
*   非首屏静态资源使用延时加载
*   SPA考虑延迟加载非首屏业务模块资源
*   缓存一切可以缓存的资源，使用MD5形式标识文件

### 请求

*   初始请求资源数 < 4 （Android支持4个，iOS 5后可支持6个）
*   初始请求资源gzip后总体积 < 50kb
*   开启Keep-Alive链路复用
*   数据离线化，考虑将数据缓存在 localStorage以及indexDB 中

## 运行时性能优化考虑

*   HTML结构语义化控制与优化，保持简单高效
*   使用CSS3动画来代替JS动画，考虑GPU加速情况
*   Display，Float等部分属性会影响页面的渲染，须合理使用
*   事件委托代理使用
*   考虑操作中的重绘与重排情
*   缓存DOM选择与计算处理，减少高级选择器以及通配选择器的使用
*   注意点击事件是300ms左右延时问题（由于需要判断是否是双击时间导致）
*   对于高频触发事件(scroll / resize / touchmove等)，进行节流与消抖
*   避免在低端机上的效果使用，考虑优雅降级以及渐进增强的形式

## 贴一张腾讯优化法则图

![image](https://isux.tencent.com/wp-content/uploads/2015/01/20150108170945527.png)

## 再看一遍雅虎军规

*   Minimize HTTP Requests - 减少HTTP请求
*   Use a Content Delivery Network - 利用CDN技术
*   Add an Expires or a Cache-Control Header - 设置头文件过期或者静态缓存
*   Gzip Components - Gzip压缩
*   Put Stylesheets at the Top - 把CSS放顶部
*   Put Scripts at the Bottom - 把JS放底部
*   Avoid CSS Expressions - 避免CSS表达式
*   Make JavaScript and CSS External - 使用JS和CSS外链
*   Reduce DNS Lookups - 减少DNS查找
*   Minify JavaScript and CSS - 压缩JS和CSS的体积
*   Avoid Redirects - 避免重定向
*   Remove Duplicate Scripts - 删除重复脚本
*   Configure ETags - 配置ETags
*   Make Ajax Cacheable - 缓存Ajax
*   Flush the Buffer Early - 尽早的释放缓冲
*   Use GET for AJAX Requests - 用GET方式进行AJAX请求
*   Post-load Components - 延迟加载组件
*   Preload components - 预加载组件
*   Reduce the Number of DOM Elements - 减少DOM元素数量
*   Split Components Across Domains - 跨域分离组件
*   Minimize the Number of iframes - 减少iframe数量
*   No 404s - 避免404页面
*   Reduce Cookie Size - 减小Cookie
*   Use Cookie-free Domains for Components - 对组件使用无Cookie的域名
*   Minimize DOM Access - 减少DOM的访问次数
*   Develop Smart Event Handlers - 开发灵活的事件处理句柄
*   Choose < link >over @import - 使用< link >而非 @import
*   Avoid Filters - 避免过滤器的使用
*   Optimize Images - 优化图片
*   Optimize CSS Sprites - 优化CSS Sprites
*   Don’t Scale Images in HTML - 不要在HTML中缩放图片
*   Make favicon. ico Small and Cacheable - 缩小favicon. ico的大小并缓存它
*   Keep Components under 25K - 保证组件在25K以下
*   Pack Components into a Multipart Document - 将组件打包进一个多部分的文档中