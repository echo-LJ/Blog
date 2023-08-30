---
title: algorithm-392： 判断子序列(双指针解法)-简单
date: 2023-08-30 10:33:22
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给定字符串 s 和 t ，判断 s 是否为 t 的子序列。字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

**示例1:**

```
输入：s = "abc", t = "ahbgdc"
输出：true
```

**示例2:**

```
输入：s = "axc", t = "ahbgdc"
输出：false
```

## 题解方法一：

**`解题思路`**

设置双指针i、j分别指向字符串s,t的首个字符，遍历字符串t;
* 当s[i] === t[j] 时，代表匹配成功， i++, j++
* 若i已经走过s的尾部，则s为t的子序列，返回true
* 当 s[i] != t[j] 时，代表匹配失败，此时仅 j++ ；
* 若t 遍历完成， s仍未遍历完成，则s不是t的子序列，返回false

**`代码实现如下：`** 
```

isSubsequence(s, t) {
    if (!s) return true;
    let i = 0;
    for (const c of t) {
      if (s[i] === c) {
        i++;
        // 若已经遍历完 s ，则提前返回 true
        if (i === s.length) {
          return true;
        }
      }
    }
    return false;
  }

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-400： 第 N 位数字-中等](https://leetcode.cn/problems/house-robber-iii/)










