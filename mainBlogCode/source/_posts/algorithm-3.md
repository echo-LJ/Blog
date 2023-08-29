---
title: leeCode-3： 无重复字符的最长子串： (滑动窗口)-中等
date: 2022-06-24 09:48:21
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。。

**示例1:**


```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例2:**

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例3:**

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

## 题解方法二：滑动窗口+哈希表

**`解题思路`**
* 哈希表dic统计： 指针j遍历字符串s, 哈希表统计s[j]最后一次出现的索引
* 更新左指针i： 根据上轮指针i和`dic[s[j]]`，每轮更新左边界i，保证区间[i+1, j]内无重复字符且最大。
* 更新结果res： 更新上轮res和本轮双指针区间j-i，取最大值。

**代码实现如下：**

```

// abcad为例
// 查找第二个a 的时候，i更新值为0，j  = 3 。
// 更新res的最大值
function lengthOfLongestSubstring(s) {
  let dic = {};
  let res = 0;
  let i = -1;
  for (let j = 0; j < s.length; j++) {
    if (s[j] in dic) {
      i = Math.max(dic[s[j]], i); // 更新左指针 i
    }
    dic[s[j]] = j; // 哈希表记录
    res = Math.max(res, j - i); // 更新结果
  }
  return res;
}
```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考地址：
* [leeCode-3： 无重复字符的最长子串： (滑动窗口)](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/)


