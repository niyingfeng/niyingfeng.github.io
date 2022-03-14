---
title: HTML5 Canvas API简介
date: 2013-06-05
tags: canvas,HTML5,html5 canvas,移动端开发
---

### canvas
canvas 其实对于HTML来说很简单，只是一个标签元素而已，自己并没有行为，但却把一个绘图 API 展现给客户端 JavaScript 以使脚本能够把想绘制的东西都绘制到一块画布上，拥有绘制路径，矩形，圆，字符以及图像等功能。 所有的标签只是图形的容器，必须使用JavaScript的 API 操作绘图。
### canvas 浏览器支持性
![](http://image.freefe.cc/20161010205158.png)
### canvas 绘图
首先是需要在页面中有 canvas 这个图形容器，为了方便在浏览器测试，直接控制台覆盖写入 canvas 标签页面、

```
document.open();
document.write();// 这边代码无法显示在编辑器下 插入一个canvas标签 
document.close();
```

接下来是使用接口获取并渲染为 2d 对象（目前只支持二维）。

```
var canvas = document.getElementById("canvas");
var cantxt = canvas.getContext("2d");
```

首先是获取到 canvas 对象，从 canvas 对象中得到二维对象进行处理。 如果使用 Object.prototype.toString 来检测 cantxt 的话， 类型为 [object CanvasRenderingContext2D] 获取了其 2d 的对象 便可以操作 canvas 了

### 首先是一些路径的 API：

`描绘路径的方法：` moveTo(x , y) : 显示的指定路径的起点为 x , y，左上角为原点，横向为 X 轴，纵向为 Y 轴。canvas 默认起点为 0 ， 0。 lineTo(x , y) : 描绘一条从起点到 (x , y) 点的直线，并且将起始位置设为 (x , y)。 rect(left , top , width , height) : 描绘一个已知左上角端点位置，以及高和宽的矩形。描绘完成后起点会一定到左上角位子。 arcTo( x1 , y1 , x2 , y2 , radius ) : 用于描绘一个与两条线段相切的圆弧，两条线段分别以当前Context绘制起点和(x2, y2)点为起点，都以(x1, y1)点为终点，圆弧的半径为radius。描绘完成后绘制起点会移动到以(x2, y2)为起点的线段与圆弧的切点。 arc( x , y , radius , startAngle , endAngle , anticlockwise ) : 用于描绘一个以(x, y)点为圆心，radius为半径，startAngle为起始弧度，endAngle为终止弧度的圆弧。anticlockwise为布尔型的参数，true表示逆时针，false表示顺时针。参数中的两个弧度以0表示0°，位置在3点钟方向；Math.PI值表示180°，位置在9点钟方向。 quadraticCurveTo( cpx , cpy , x , y) : 以当前Context绘制起点为起点，(cpx,cpy)点为控制点，(x, y)点为终点的二次样条曲线路径。 bezierCurveTo( cpx1 , cpy1 , cpx2 , cpy2 , x , y); ： 以当前Context绘制起点为起点，(cpx1,cpy1)点和(cpx2, cpy2)点为两个控制点，(x, y)点为终点的贝塞尔曲线路径。 `在描绘完路径之后，还需要使用以下方法 绘制路径或者填充颜色：` stroke() : 按照路线绘线条。 fill() : 使用当前设置好的 style 来填充路径区域。 clip() : 按照已有的路线在画布中设置剪辑区域，调用后图形编辑代码只会对编辑区域有效，对外界无效。如未调用则就是当前整个 canvas 为编辑区域。 `e.g.`

```
cantxt.rect(50,50,40,80);
cantxt.fillStyle = "#0F0"; 
cantxt.fill();

cantxt .moveTo(50,50); // 移动到坐标 50 50 
cantxt.lineTo(150,150); // 划出轨迹到 150 150
cantxt.stroke(); // 以线条显示轨迹

// 突然会发现，颜色方面有点错误，线条的黑色会影响到矩形的绿色。所以需要使用 打开路径 关闭路径的方法隔断路径间的联系。

cantxt.beginPath(); 
cantxt.rect(50,50,50,100);
cantxt.fillStyle = "#0F0"; 
cantxt.fill();
cantxt.closePath();

cantxt.beginPath(); 
cantxt .moveTo(50,50); // 移动到坐标 50 50 
cantxt.lineTo(150,150); // 划出轨迹到 150 150
cantxt.stroke(); // 以线条显示轨迹
cantxt.closePath();

cantxt.beginPath();
cantxt.moveTo(50,50); 
cantxt.arcTo(100,100,200,50,50);
cantxt.stroke(); 
cantxt.closePath();
```

以及其他的几个方法： fillText(text , left , top , [maxWidth]) : 字符串，相对与原点的坐标，字符串的最大长度。其中最大长度maxWidth是可选参数。

```
cantxt.fillText("abcde",100,300);
```

`还有一种方式是不使用路径，直接填上颜色：` fillRect( left , top , width , height ) : 直接填充矩形。 strokeRect( left , top , width , height ) : 划出矩形边框。 clearReck(left , top , width , height ) : 清除矩形内所有内容。

```
cantxt.strokeRect( 50,50,100,100 );
cantxt.clearRect( 50,50,100,100 ); // 发现还会留下一圈边框
cantxt.clearRect( 49,49,102,102 );
```

fillRect没有上面的问题。 `还有几个可以设置的属性：` strokeStyle : 线条颜色，默认为 "#000000"，可设为 css颜色值，渐变对象，或者模式对象。 fillStyle : 填充的颜色。 lineWidth : 线条宽度。单位 px。 lineCap : 线条端点样式， butt 无，round 圆头， square 方头。 lineJoin : 线条转折的处理，round 圆角 , bevel 平角 , miter 尖角。 miterLimit : 尖角的锐利程度，默认 10. translate（x,y）：平移变换，原点移动到坐标（x，y）； rotate（a）：旋转变换，旋转a度角； scale（x，y）：伸缩变换； save（），restore（）：提供和一个堆栈，保存和恢复绘图状态，save将当前绘图状态压入堆栈，restore出栈，恢复绘图状态；

```
cantxt.translate(200,200);
cantxt.rotate(1);
cantxt.moveTo(0,0);
cantxt.lineTo(100,100); 
cantxt.stroke();
```

`Context对象中拥有drawImage()方法可以将外部图片绘制到Canvas中。` drawImage()方法的3种原型如下： drawImage(image, dx, dy); drawImage(image, dx, dy,dw, dh); drawImage(image, sx, sy,sw, sh, dx, dy, dw, dh); 其中，image参数可以是HTMLImageElement、HTMLCanvasElement或者HTMLVideoElement。第三个方法原型中的sx、sy在前两个中均为0，sw、sh均为image本身的宽和高；第二和第三个原型中的dw、dh在第一个中也均为image本身的宽和高。