---
title: 简单的快速排序
date: 2012-08-05
tags: 快速排序,算法
---

对于数字的从大到小排序，我们应该依旧记得，刚刚学习编程语言是那种经典的算法-冒泡法。那属于一种简单的但是并不高效的算法。 今天来说一下另一种简单的算法-快速排序。快速排序的思想是，将数字头作为base数，进行判断排序后将小于base数的置于base数左侧，大于base数的置于base数右侧，再以现在的base数作基准，将左右2侧的子数组同样处理。 第一步方法为：

```
function division(arr,l,r){
    var base = arr[l];
    while(l<r){
        while(l<r&&arr[r]>=base){
            r--;
        }
        arr[l] = arr[r];

        while(l<r&&arr[l]<=base){
            l++;
        }
        arr[r] = arr[l];
    }
    arr[l] = base;
    return l;
}
```

第二步为迭代循环：

```
function list(arr,l,r){
    if(l<r){
        var i = division(arr,l,r);
        arguments.callee(arr,l,i-1);
        arguments.callee(arr,i+1,r);
    }
}
```

使用它：

```
var arr = [92,23,45,22,53,74,24,7];
list(arr,0,7);
```

这里是在原数组进行处理的。