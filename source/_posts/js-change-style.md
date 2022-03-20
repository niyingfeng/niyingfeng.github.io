---
title: 关于使用js动态修改样式问题
date: 2012-05-15
tags: 
- CSS修改样式
---

有时我们需要动态生成效果，那么就需要对页面的css进行各种处理，进而达到一些样式的改变。如动画，拖放等效果。所以使用JS正确的来对页面样式修改是灰常重要的。
### 1.修改标签的class属性值
直接在css中定义多种css类型 然后在事件中对DOM对象的 class属性进行切换，是最简单可行的一种方式。也是目前使用最多的方法。 当然最简单的就是使用

```
```
<script type="text/javascript">
    var obj = document.getElementById("div");
    obj.className = "otherclass";
</script>
```
```

非标准ECMAScript来进行修改其class的值。 还有就是使用ES标准来进行修改，就是使用DOM的setAttribute方法。但是他有一个兼容性问题。

```
```
<script type="text/javascript">
    var obj = document.getElementById("div");
    obj.setAttribute("className","otherclass");//IE下使用className
    obj.setAttribute("class","otherclass");//FF下的方式 所以要注意
</script>
```
```

一个简单的小问题，但也需要注意。
### 2.添加行内样式进行覆盖
直接使用 dom_obj.style.*** = "***"; 来进行对其样式的覆盖。由于行内样式优先级最高，所以能覆盖其他节点的样式。 该方法也是极为常用的，唯一需要注意的就是对于样式名的大小写问题。如：border-left 应该为 borderLeft 使用该方法我们可能需要用到计算当前样式值的方法。这里也具有兼容性问题。 currentStyle只有IE支持。取得当前应用的样式。 getComputedStyle支持FF，Opera，Safari，Chrome等浏览器。取得最终应用的样式。

```
```
<script type="text/javascript">
    var obj = document.getElementById("div");
    objh=document.defaultView.getComputedStyle(obj,null).height;
    //或者
    objh=window.getComputedStyle(obj,null)[height];
    //IE下 需要
    objh=obj.currentStyle[height];
</script>
```
```

下面是一个解决兼容性方法

```
```
function getStyle(element,property) {
    var value = element.style[camelize(property)];
    if(!value) {
        if(document.defaultView && document.defaultView.getComputedStyle) {
            value = document.defaultView.getComputedStyle(element).getPropertyValue(property);
        } else if(element.currentStyle) {
            value = element.currentStyle[camelize(property)];
         }
    }
    return value;
}
```
```

其实 setAttribute方法 是有3个参数的。

```
```
object . setAttribute ( sName , vValue , iFlags )
```
```

参数： sName : 必选项。字符串(String)。指定属性的名称。 vValue : 必选项。要赋给属性的值。可以是任何需要的变量类型。 iFlags : 可选项。整数值(Integer)。0 | 1 0 : 当属性被设置的时候，对象任何已有的同名属性设置都会被覆盖，而不会考虑它们的大小写。 1 : 默认值。执行严格考虑字母大小写的属性设置。假如此方法的 iFlags 参数设置为 1 ，则执行 iFlags 参数设置为 0 的 getAttribute 方法时，满足 sAttrName 指定的特性名称不一定能被找到。
### 3.修改样式表
获取文档样式表的lists document.styleSheets[0];//获取第一个样式表 W3C坚持使用cssRules[]，而微软坚持rules[]。 document.styleSheets[0].cssRules[0];//W3C 获取第一个样式表的第一个样式信息 document.styleSheets[0].rules[0];//IE 获取第一个样式表的第一个样式信息 更多信息请查询 document.styleSheets 的相关信息。
### W3C DOM2样式规则

### CSSStyleSheet对象

CSSStyleSheet对象表示的是所有CSS样式表，包括外部样式表和使用标签指定的嵌入式样式表。 CSSStyleSheet同样构建于其他的DOM2 CSS对象基础之 上，而CSSStyleRule对象表示的则样式表中的每条规则。 通过document.stylesheets属性可以取得文档中CSSStyleSheet对象的列表，其中每个对象有下列属性

```
```
type　　　　　　　　始终为text/css
disabled　　　　　　相应样式表是应于还是禁用于当前文档
href　　　　　　　　样式表相对于当前文档的URL
title　　　　　　　　分组样式标签
media　　　　　　   样式应用的目标设备类型(screen、print)
ownerRule　　　　   只读CSSRule对象，若样式用@import导入，表示其父规则
cssRules 　　　　　只读cssRuleList列表对象，包含样式表中所有CSSRule对象

insertRule(rule,index)　　　　添加新的样式声明
deleteRule(index)　　　　　 从样式表中移除规则
```
```

### CSSStyleRule对象

每个CSSStyleSheet对象内部包含着一组CSSStyleRule对象。这些对象分别对应着类似下面这样一条规则：

```
```
body{
    font:lucida,verdana,sans-serif;
    background:#c7600f;
    color:#1a3800;
}
```
```

CSSStyleRule对象具有下列属性：

```
```
type　　　　　　　　继承自CSSRule对象的一个属性，以0~6中的一个数字表示规则类型
cssText　　　　　　 以字符串形式表示的当前状态下的全部规则
parentStyleSheet　　 引用父CSSStyleRule对象
parentRule　　　　　 如果规则位于另一规则中，该属性引用另一个CSSRule对象
selectorText　　　　   包含规则的选择符
style　　　　　　　　与HTMLElement.style相似，是CSSStyleDeclaration对象的一个实例
```
```

### CSSStyleDeclaration对象

表示一个元素的style属性，与CSSStyleRule对象类似，CSSStyleDeclaration具有下面属性： cssText　　　　以字符串形式表示的全部规则 parentRule　　 将引用CSSStyleRule对象

```
```
getPropertyValue(propertyName)　　　　　　CSS样式属性值
removeProperty(propertyName)　　　　　　  从声明中移除属性
setProperty(propertyName,value,priority)　　   设置CSS属性值
```
```