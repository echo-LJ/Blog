---
title: 使用js判断给定的一组数字是否连续?
date: 2022-10-21 09:19:49
tags: JS
---


<meta name="referrer" content="no-referrer"/>

## 场景

假如有一组数组，如`[1,2,3,4,5,6]`，可以看出这数组是连续的，那么如`[1,2,3,5,6]`，这串数组就不是连续的了，用js写判断方法

## 实现

```
function isContinuityNum(num){
    let array = []
    if(num instanceof Array) {
        array = [...num]
    } else {
        array = Array.from(num.toString())
    }

    var i = array[0]
    var isContinuation = true;
    for (var e in array) {
        if(array[e] !== i) {
            isContinuation = false
            break;
        }
        i++
    }
    return isContinuation
}
```