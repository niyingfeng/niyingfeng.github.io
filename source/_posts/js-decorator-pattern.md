---
title: JS设计模式七：装饰者模式
date: 2014-07-20
tags: 
- JS设计模式
- 设计模式
---

装饰者模式，Decorator Pattern。在不改变原类和继承的情况下动态扩展对象功能，通过包装一个对象来实现一个新的具有原对象相同接口的新的对象。

**装饰者模式特点：**

1. 不修改原对象的原本结构来进行功能添加。
2. 装饰对象和原对象具有相同的接口，可以使客户以与原对象相同的方式使用装饰对象。
3. 装饰对象中包含原对象的引用，即装饰对象为真正的原对象在此包装的对象。

装饰者模式可以为对象添加功能从而代替了编写大量子类的情况。

以JS设计模式实例为例，首先自行车商店有4种类型自行车：

```
var ABicycle = function(){ ... };
var BBicycle = function(){ ... };
var CBicycle = function(){ ... };
var DBicycle = function(){ ... };
```

当自行车商店需要为每类自行车推出附加功能或者配件，比如前灯，车篮，改色等的时候，其最基本的方式就是为每一种组合创建一个子类，如有铃铛的A类车，黄色的B类车，有车篮的C类车等等，

```
var ABicycleWithBell = function(){ ... };
var BBicycleWithColorYellow = function(){ ... };
var CBicycleWithColorBule = function(){ ... };
...
```

的确，就犹如你第一眼看到的感觉一样，这搞法果断不靠谱，n种配置，到最后就会生成4n+4种类（包括原始的4个父类），那是决然不可取的。

当然我们可能的第一想法便是可以将颜色，车篮什么的设为实例属性，在类中由传递的参数进行控制生成，这样的确不错没什么问题，不过对于一些功能上的扩展可能就心有余而力不足了，比如自行车添加变速功能，刹车加强功能，防盗功能等，那么对于添加这些功能使用装饰者模式则更为简便了（如果说将更能内置于类中，再由传参进行处理，在功能较多的情况下显然也比较不靠谱）。

首先需要一个基础自行车A的类

```
function ABicycle(){ }
ABicycle.prototype = {
    wash : function(){ },
    ride : function(){ },
    getPrice : function(){ 
        return 999; 
    }
}
```

接下来就是给自行车装上铃铛，响铃的功能：

最简单的应该就是直接包装对象实例：

```
function bicycleBell( bicycle ){
    var price= bicycle.getPrice();

    bicycle.bell = function(){
        console.log("ding! ding! ding!");
    };

    bicycle.getPrice = function(){
            return price + 100;
    };
    return bicycle;
}
```

那么使用

```
var bicycleA = new ABicycle();
bicycleA = bicycleBell( bicycleA );
```

如果是下面这种形式

```
bicycle.getPrice = function(){
     return bicycle.getPrice() + 100;
};
```

这种形式当然就会导致无限循环调用~

的确这种包装方法不是将对象再次包装成构造函数，仅仅只是调整实例对象，既方便又简单，不过对于类似getPrice方法这种返回常量数据结果的倒是问题也不大，可以使用如上面闭包形式的方式来保存。

但如果对于其功能的在包装需要调用，再用闭包形式来先进行原始方法的保存，就会赶脚很繁琐不变。

那么可以来看一下下面这种装饰方式：

```
function BicycleBell( bicycle ){
    this.bicycle = bicycle;
}

BicycleBell.prototype = {
    wash : function(){ 
        return this.bicycle.wash();
    },
    ride : function(){
        return this.bicycle.ride();
    },
    getPrice : function(){ 
        return this.bicycle.ride() + 100; 
    },
    bell : function(){
        console.log("ding! ding! ding!");
    }
}
```

包装实例，再次模拟原始类，将实例作为参数包装，提供与原始类一样的接口。这种方式很好的解决了对于某些需要修改并且依赖原始该方法的方法生成形式。当然其也有自己的缺点，这个装饰者太过于繁琐，所有的，比如加速，切换颜色，车篮什么的装饰者每一个都必须如此形式的话势必代码有点杂乱多。

那么，提取出来吧

首先需要有一个继承的方法，并且有指向父类的prototype

```
function extend( subClass, superClass ){
    var F = function(){};
    F.prototype = superClass.prototype;
    subClass.prototype = new F();
    subClass.prototype.constructor = subClass;
    
    // 此处指向superClass的prototype 比直接保存superClass使用更为方便
    subClass.superclass = superClass.prototype;
    if( superClass.prototype.constructor === Object.prototype.constructor ){
        superClass.prototype.constructor = superClass;
    }
}
```

再来一个各个装饰者可以依赖继承的中间装饰者：

```
function BicycleDecorator( bicycle ){
    this.bicycle = bicycle;
}
BicycleDecorator.prototype = {
    wash : function(){ 
        return this.bicycle.wash();
    },
    ride : function(){
        return this.bicycle.ride();
    },
    getPrice : function(){ 
        return this.bicycle.ride(); 
    }
}
```

然后么很巧妙的使用extend

```
var BicycleBell = function( bicycle ){
    // 继承 BicycleDecorator 内部 this定义的数据或者方法
    BicycleBell.superclass.constructor.call( this, bicycle );
}
// 继承 BicycleDecorator.prototype 并且添加 BicycleBell.superclass 指向 BicycleDecorator.prototype
extend( BicycleBell, BicycleDecorator );
// 添加或者修改
BicycleBell.prototype.bell = function(){
    console.log("ding! ding! ding!");
}
BicycleBell.prototype.getPrice = function(){
    return this.bicycle.getPrice() + 100;
}
```

一样的方式进行实例包装使用

```
var bicycleA = new ABicycle();
bicycleA = new BicycleBell( bicycleA );

```
上述方式就是一种较为不错的装饰者模式形式，更完善一些可能需要对BicycleDecorator，以及BicycleDecorator.prototype对象定义时使用对最初类遍历方式来获取各个原有接口。

当然对于这几种方式可以按需进行使用，有针对性的处理才是最有方案。