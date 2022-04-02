---
title: JavaScript
date: 2021-01-26 16:56:51
tags:
---
<meta name="referrer" content="no-referrer"/>
## 1.用reduce实现map方法
**reduce**
* reduce是一个累加方法，是对数组累积执行回掉函数，返回最终计算的结果。
```
array.reduce(function(total, currentVal, currentIndex, arr) {}, initialValue)
```
1. total必需：初始值，或者是计算结束之后的返回值
2. currentVal必需：当前元素
3. currentIndex可选：当前元素的索引
4. arr可选： 当前元素的所属数组
5. initialValue可选： 传给函数的初始值
**map**
map是遍历数组每一项，并执行回调函数的操作，返回一个对每一项进行操作的新数组。
```
array.map(function(currentVal, index, arr){}, thisArg)
```
1. currentVal必需：当前元素
2. index可选：当前元素的索引
3. arr可选： 当前元素的所属数组
4. thisArg可选：对象作为该执行回调时使用，传递给函数，用作‘this’的值，如果省略了thisArg，或者传入了null,undefined,那么回掉函数的this为全局对象。
```
Array.prototype.myMap = function (fn,thisArg = []) {
  if (typeof fn !== "function" ) {
    throw new Error(`${fn} is not a function!`)
  }
  const res = [];
  this.reduce((pre, cur, index, arr) => {
    res.push(fn.call(thisArg, cur, index, arr))
  },[])
  return res
}
```

## 2. 介绍instanceof的原理及实现
instanceof主要用来判断某个实例是否属于某个类型，或者用来判断某个实例是否是其父类型或者祖先类型的实例
**实现原理**： 只要右边变量的prototype在左边的原型链上即可。
因此instanceof在查找过程中会遍历左边变量的原型链，直到找到右边的prototype，如果失败，则返回false.
```
function myInstanceof(left, right) {
  if (typeof right !== "function" ) throw newError('instance error')
  if (!left || (typeof left !== 'object' && typeof right !== 'function')) {
    return false
  }
  // 获取原型对象
  const rightVal = right.prototype
  let leftVal = left._proto_
  while (leftVal) {
    if (leftVal == rightVal) return true;
    leftVal = leftVal._proto_
  }
  return false；
}
```
