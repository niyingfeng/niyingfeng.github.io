---
title: IE6关于getElementById的BUG
date: 2012-08-02
tags: IE bug,IE6
---

最近遇到一个很常见的JS在IE下的兼容性问题，其实也很简单，主要由于IE6下调试工具较少不好发现导致花费了挺多的时间。 首先说一下我遇见的关于getElementById的小小兼容性问题吧，都知道 document.getElementById 是获取DOM节点中指定id的节点，但是在IE6下，会把某些标签中（并不是所有标签）的name也当做 getElementById 可获取的元素，有时真会发生影响不到的坑。 我是在获取相应id节点后再插入一个div，没想到在head的标签内竟然将其name属性设置了可这个ID一样的名字。真是妹的我去了！！！IE6中只是看到该div不显示，真不容易发现其错误。关键是标签内无内容，弹出其innerHTML还未空。呵呵~ 下面是一些有意思的代码： 下面代码在IE6下会获取正常的DOM节点：

```
<body>
        <p name="abc" id="cba">
                这是第1个div
        </p>
        <div id="abc">
                这是第2个div
        </div>
        <script language=javascript>
                alert(document.getElementById("abc").id);
        </script>
</body>
```

而下面代码就会有问题：

```
<body>
        <a name="abc" id="cba" href="http://www.baidu.com">
                这是第1个div
        </a>
        <div id="abc">
                这是第2个div
        </div>
        <script language=javascript>
                alert(document.getElementById("abc").id);
        </script>
</body>
```

所以，对于IE6下 getElementById方法出现BUG也是针对于相关标签的。包括 meta标签 a标签 form标签以及一些相关表单元素。 主要解决方法是 注意整体的name使用，不要将name与id值混淆使用，这个是最优方式。 还有就是方法判断，这是司徒正南给的：

```
var getElementById = function(id){
    var el = document.getElementById(id);
    if(!+"\v1"){
        if(el && el.attributes['id'].value === id){
            return el
        }else{
            var els = document.all[id],n = els.length;
            for(var i=0;i<n;i++){
                if(els[i].attributes['id'].value === id){
                    return els[i]
                }
            }
        }
    }
    return el;
}
```