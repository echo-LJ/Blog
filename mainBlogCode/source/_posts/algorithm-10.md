---
title: leeCode-10： 正则表达式匹配-困难
date: 2023-08-01 16:58:50
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

1、'.'匹配任意单个字符
2、'*'匹配零个或多个前面的那一个元素
所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。
**示例1:**


```
输入：s = "aa", p = "a"
输出：false
解释："a" 无法匹配 "aa" 整个字符串。
```

**示例2:**

```
输入：s = "aa", p = "a*"
输出：true
解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```

**示例3:**

```
输入：s = "ab", p = ".*"
输出：true
解释：".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
```


## 题解方法一：

**`解题思路`**
* 首部空格： 删除即可。
* 符号位： 三种情况，即 '+' , '−' , ''无符号" ；新建一个变量保存符号位，返回前判断正负即可。
* 非数字字符： 遇到首个非数字的字符时，应立即返回。
* 数字字符：
    1、字符转数字： “此数字的 ASCII 码” 与 “ 000 的 ASCII 码” 相减即可。Js用Number转换即可
    2、数字拼接： 若从左向右遍历数字，设当前位字符为 c ，数字结果为 res，则数字拼接公式为：`res=10×res+Number(c)`
    
**代码实现如下：**
```
var myAtoi = function(s) {
    s = s.trim(); // 删除首尾空格
    if (!s) return 0; // 字符串为空则直接返回
    let res = 0, i = 1, sign = 1;
    const int_max = Math.pow(2, 31) - 1 //2147483647
    const  int_min = -Math.pow(2, 31)  // -2147483647
    bndry = Math.floor(Math.pow(2, 31) / 10); // 214748364 //boundary边界值
    if (s[0] === '-') {
      // 保存负号
      sign = -1; 
    } else if (s[0] !== '+'){
      // 若无符号位，则需从 i = 0 开始数字拼接
       i = 0; 
    }
    
    for (let j = i; j < s.length; j++) {
      const c = s[j];
      if (!('0' <= c && c <= '9')) break; // 遇到非数字的字符则跳出
      // res >214748364 终止循环 已进入最大值
      // res === bndry && c > '7' 终止循环 已进入最大值， 否则res会比2147483647还要大
      if (res > bndry || (res === bndry && c > '7')) return sign === 1 ? int_max : int_min; // 数字越界处理
      res = 10 * res +  Number(c); // 数字拼接
    }
    return sign * res;
  }


```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [正则表达式匹配-困难](https://leetcode.cn/problems/regular-expression-matching/solutions/2361807/10-zheng-ze-biao-da-shi-pi-pei-dong-tai-m5z1i/)


**`Array.from():`** 静态方法从可迭代或类数组对象创建一个新的浅拷贝的数组实例。
* Array.from(arrayLike)
* Array.from(arrayLike, mapFn
```
console.log(Array.from('foo'));
// Expected output: Array ["f", "o", "o"]

console.log(Array.from([1, 2, 3], x => x + x));
// Expected output: Array [2, 4, 6]

```
 