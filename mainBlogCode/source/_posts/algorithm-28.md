---
title: leeCode-28：找出字符串中第一个匹配项的下标（朴素解法、少了KMP算法）-简单
-简单
date: 2023-08-09 14:21:26
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串的第一个匹配项的下标（下标从 0 开始）。如果 needle 不是 haystack 的一部分，则返回  -1 。

**示例1:**
```
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0 ，所以返回 0 。
```

**示例2:**

```
输入：haystack = "leetcode", needle = "leeto"
输出：-1
解释："leeto" 没有在 "leetcode" 中出现，所以返回 -1 。
```

## 题解方法一：朴素解法


**`解题思路`**

直观的解法的是：枚举原串 s 中的每个字符作为「发起点」，每次从原串的「发起点」和匹配串的「首位」开始尝试匹配：
* 匹配成功：返回本次匹配的原串「发起点」。
* 匹配失败：枚举原串的下一个「发起点」，重新尝试匹配。


**代码实现如下：**
```
var strStr = function(haystack, needle) {
    const n = haystack.length, m = needle.length;
  const s = haystack.split(''), p = needle.split('');
  // 枚举原串的「发起点」
  for (let i = 0; i <= n - m; i++) {
    // 从原串的「发起点」和匹配串的「首位」开始，尝试匹配
    let a = i, b = 0;
    while (b < m && s[a] === p[b]) {
      a++;
      b++;
    }
    // 如果能够完全匹配，返回原串的「发起点」下标
    if (b === m) return i;
  }
  return -1;
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [leeCode-27： 移除元素（双指针）-简单](https://leetcode.cn/problems/remove-element/description/)