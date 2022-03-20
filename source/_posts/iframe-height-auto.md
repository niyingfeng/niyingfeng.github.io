---
title: iframe自适应高度
date: 2014-09-09
tags: 
- iframe高度
- iframe高度自适应
---

iframe 自适应高度，目前最广泛的实现方式是以 JS 获取 iframe 所载入页面的 body 高度。（在本地 html 文件测试的时候 chrome 会有跨域错误，可以使用 IE 来测试）。其实质就是通过 offsetHeight 或者 scrollHeight 获取到 iframe 内部 body 的高度，再调整 iframe 高度即可。

以下是Test.html 源码初略形式：

```
<!DOCTYPE HTML>
<html>
  <head>
    <title>iframe 自适应高度</title>
    <meta charset="UTF-8"/>
  </head>
  <body>
    <iframe id="auto" name="auto" src="./Test2.html" width="100%" onload="autoHeight()"></iframe>
    <script type="text/javascript" charset="utf-8">
      function autoHeight(){ 
        var ifr = document.getElementById('auto'); 
        ifr.height = ( ifr.contentDocument && ifr.contentDocument.body.offsetHeight ) ||
                     ( ifr.contentWindow.document.body.scrollHeight ); 
      }
    </script>
  </body>
</html>
```

Test2.html 源码

```
<!DOCTYPE HTML>
<html> 
  <head>
    <title>iframe 自适应高度</title>
    <meta charset="UTF-8"/>
    <style type="text/css">
      html,body{
        margin: 0;
        padding: 0;
      }
      #block{
        background-color: red;
        height: 500px;
        width: 800px;
      }
    </style>
  </head>
  <body>
    <div id="block"> </div> 
  </body>
</html>
```

当然以上只是简单的实现，具体更严谨的计算高度做好使用一些兼容性不错库作为基础。