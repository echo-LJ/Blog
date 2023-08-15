---
title: leeCode-242： 有效的字母异位词： (哈希表解法)-中等
date: 2023-08-15 11:15:49
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。
注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。

**示例1:**

```
输入: s = "anagram", t = "nagaram"
输出: true
```

**示例2:**

```
输入: s = "rat", t = "car"
输出: false
```


## 题解方法一：双指针双次循环

**`解题思路`**
* 设两字符串 s1,s2，则两者互为重排的「充要条件」为：两字符串s1,s2包含的字符是一致的，即 s1,s2所有对应字符数量都相同，仅排列顺序不同。

* 可借助「哈希表」分别统计 s1,s2中各字符数量 key: 字符, value: 数量 ，分为以下情况：

1、若 s1,s2 字符串长度不相等，则「不互为重排」；
2、若 s1,s2 某对应字符数量不同，则「不互为重排」；
3、否则，若  s1,s2 所有对应字符数量都相同，则「互为重排」；

**`代码实现如下：`** 
```
function isAnagram(s, t) {
  if (s.length !== t.length) {
    return false;
  }
  const dic = new Map();
  for (let i = 0; i < s.length; i++) {
    dic.set(s[i], (dic.get(s[i]) || 0) + 1);
  }
  for (let i = 0; i < t.length; i++) {
    dic.set(t[i], (dic.get(t[i]) || 0) - 1);
  }
  for (const val of dic.values()) {
    if (val !== 0) {
      return false;
    }
  }
  return true;
}
```


参考链接：

* [leeCode-242： 有效的字母异位词： (哈希表解法)-中等](https://leetcode.cn/problems/valid-anagram/description/)
