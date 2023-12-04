---
title: algorithm-65： 有效数字(有限状态自动机解法)-困难
date: 2023-09-04 13:53:24
tags: 算法-未解
---

<meta name="referrer" content="no-referrer"/>

## 题目描述：有效数字（按顺序）可以分成以下几个部分：

1. 一个 小数 或者 整数
2. （可选）一个 'e' 或 'E' ，后面跟着一个 整数

**小数**（按顺序）可以分成以下几个部分：
1. （可选）一个符号字符（'+' 或 '-'）
2. 下述格式之一：
    - 至少一位数字，后面跟着一个点 '.'
    - 至少一位数字，后面跟着一个点 '.' ，后面再跟着至少一位数字
    - 一个点 '.' ，后面跟着至少一位数字
**整数**（按顺序）可以分成以下几个部分：
1. （可选）一个符号字符（'+' 或 '-'）
2. 至少一位数字

**给你一个字符串 s ，如果 s 是一个 有效数字 ，请返回 true 。**

**示例1:**
```
输入：s = "0"
输出：true
```

**示例2:**
```
输入：s = "e"
输出：false
```

**示例3:**
```
输入：s = "."
输出：false
```

## 题解方法一：逐位查找

**`解题思路`**
本题使用**有限状态自动机** 根据字符类型和合法数值的特点，先定义状态，再画出状态转移图，最后编写代码即可。
字符类型：`空格 「 」`、`数字「 0—90—90—9 」` 、`正负号 「 +++, −-− 」 `、`小数点 「 ... 」` 、`幂符号 「 eee, EEE 」` 。
状态定义：按照字符串从左到右的顺序，定义以下 9 种状态。

* 0 : 开始的空格
* 1 :正负号
* 2: 小数点前的数字
* 3: 小数点、小数点后的数字
* 4: 当小数点前为空格时，小数点、小数点后的数字
* 5 :幂符号
* 6: 幂符号后的正负号
* 7: 幂符号后的数字
* 8: 结尾的空格

结束状态：合法的结束状态有 2, 3, 7, 8 。

**`算法过程`**
1. 初始化:
    - 状态转移表states：  设 states[i], 其中 i 为所处状态， states[i] 使用哈希表存储可转移至的状态.键值对 (key, value) 含义：输入字符 key ，则从状态 i 转移至状态 value 。
    - 当前状态 p ： 起始状态初始化为 p = 0 。


**`代码实现如下：`**
```
/**
 * @param {string} s
 * @return {boolean}
 */

function isNumber(s) {
    const states = [
        { ' ': 0, 's': 1, 'd': 2, '.': 4 },    // 0. start with 'blank'
        { 'd': 2, '.': 4 },                    // 1. 'sign' before 'e'
        { 'd': 2, '.': 3, 'e': 5, ' ': 8 },    // 2. 'digit' before 'dot'
        { 'd': 3, 'e': 5, ' ': 8 },            // 3. 'digit' after 'dot'
        { 'd': 3 },                            // 4. 'digit' after 'dot' ('blank' before 'dot')
        { 's': 6, 'd': 7 },                    // 5. 'e'
        { 'd': 7 },                            // 6. 'sign' after 'e'
        { 'd': 7, ' ': 8 },                    // 7. 'digit' after 'e'
        { ' ': 8 }                             // 8. end with 'blank'
    ];
    
    let p = 0;  // start with state 0
    for (let c of s) {
        let t;
        if (c >= '0' && c <= '9') t = 'd';      // digit
        else if (c === '+' || c === '-') t = 's'; // sign
        else if (c === 'e' || c === 'E') t = 'e'; // e or E
        else if (c === '.' || c === ' ') t = c; // dot, blank
        else t = '?';                          // unknown
        
        if (!(t in states[p])) return false;
        p = states[p][t];
    }
    
    return [2, 3, 7, 8].includes(p);
}
```
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [leeCode-70： 爬楼梯(动态规划)-简单](https://leetcode.cn/problems/climbing-stairs/submissions/)

