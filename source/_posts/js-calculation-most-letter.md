---
title: JS 统计一个字符串中出现次数最多字母
date: 2014-09-15
tags: 
- JS算法
---

一个感觉很平凡的，常常在面试中出现的题目，拥有各种实现形式也就显示出 JS 水平的不同。

首先，简单的来一个基本思路的计算方式：

```
function maxNumLetter( str ){
    var lettersObj = {},
        len = str.length,
        letter, letterNum, maxLetter, maxNumber = 0;


    while( len-- ){
        letter = str.substr(len, 1);
        letterNum = lettersObj[ letter ] = ( lettersObj[ letter ] || 0 ) + 1;
        
        if( letterNum > maxNumber ){
            maxLetter = letter;
            maxNumber = letterNum;
        }else if( letterNum === maxNumber ){
            ( maxLetter instanceof Array) ? maxLetter.push( letter ) : ( maxLetter = [ maxLetter, letter ] );
        }
    }
    return maxLetter.toString();
}
```

上面是基本实现形式，对字符串的每个字母进行遍历，并且在 lettersObj 进行缓存记录，不过每次循环都对字符串进行截取字母看着总是有点不爽，那么可以先将字符串通过 split 进行数组化在进行循环遍历，或者使用字符串的 replace 方式进行处理：

```
function maxNumLetter( str ){
    var lettersObj = {},
        letterNum, maxLetter, maxNumber = 0;


    str.replace(/[a-zA-Z]/g, function( l ){
        var lNum = (lettersObj[ l ] || 0 ) + 1;
              letterNum = lettersObj[ l ] = ( lettersObj[ l ] || 0 ) + 1;
       
        if( letterNum > maxNumber ){
              maxLetter = l;
              maxNumber = letterNum;
        }else if( letterNum === maxNumber ){
              ( maxLetter instanceof Array) ? maxLetter.push( l ) : ( maxLetter = [ maxLetter, l ] );
        }
    });
    return maxLetter.toString();
}
```

使用 replace 添加函数参数形式的特性来替代人工的字母遍历循环。当然由于考虑有相同最多次数字母情况，所以显得比较繁琐。如果还有较为巧妙的方法，欢迎学习交流~