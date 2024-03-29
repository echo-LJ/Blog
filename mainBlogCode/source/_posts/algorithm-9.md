---
title: leeCode-9：回文数-简单
date: 2023-07-04 21:10:15
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你一个整数 x ，如果 x 是一个回文整数，返回 true ；否则，返回 false 。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

* 例如，121 是回文，而 123 不是。

**示例1:**


```
输入：x = 121
输出：true
```

**示例2:**

```
输入：x = -121
输出：false
解释：从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

**示例3:**

```
输入：x = 10
输出：false
解释：从右向左读, 为 01 。因此它不是一个回文数。
```

## 题解方法一：反转一半数字

**`解题思路`**
* 如果是负数则一定不是回文数，除了 0 以外，所有个位是 0 的数字不可能是回文，因为最高位不等于 0,直接返回 false。
* 如果是正数，则将其倒序数值计算出来，然后比较和原数值是否相等
* 由于整个过程我们不断将原始数字除以 10，然后给反转后的数字乘上 10，所以，当原始数字小于或等于反转后的数字时，就意味着我们已经处理了一半位数的数字了,如果是回文数则相等返回 true，如果不是则不相等 false
* 比如 123 的倒序 321，不相等；121 的倒序 121，相等


![9_fig1.png](https://upload-images.jianshu.io/upload_images/11846892-4220766a58f0b55b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现如下： 
```
var isPalindrome = function(x) {
    // 特殊情况：
    // 如上所述，当 x < 0 时，x 不是回文数。
    // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
    // 则其第一位数字也应该是 0
    // 只有 0 满足这一属性

    if(x<0 || (x % 10 ==0) && x!== 0) return false;
    let cur = 0;
    while(x > cur) {
        cur = cur * 10 + x % 10
        x = Math.floor(x/10)
    }
     // 当数字长度为奇数时，我们可以通过 revertedNumber/10 去除处于中位的数字。
    // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，revertedNumber = 123，
    // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。

    return cur == x || x === Math.floor(cur/10)
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [力扣-回文数-简单](https://leetcode.cn/problems/palindrome-number/solutions/281686/hui-wen-shu-by-leetcode-solution/)