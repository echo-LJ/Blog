---
title: leeCode-205 同构字符串 (哈希表解法)-初级
date: 2023-08-27 16:57:14
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定两个字符串 s 和 t ，判断它们是否是同构的。如果 s 中的字符可以按某种映射关系替换得到 t ，那么这两个字符串是同构的。每个出现的字符都应当映射到另一个字符，同时不改变字符的顺序。不同字符不能映射到同一个字符上，相同字符只能映射到同一个字符上，字符可以映射到自己本身。



**示例1:**


```
输入：s = "egg", t = "add"
输出：true
```

**示例2:**
```
输入：s = "foo", t = "bar"
输出：false
```
**示例3:**
```
输入：s = "paper", t = "title"
输出：true
```

## 题解方法一：哈希表

**`理解题意`**
* 每个出现的字符都应当映射到另一个字符： 代表字符集合 s , t 之间是「满射」。
* “相同字符只能映射到同一个字符上，不同字符不能映射到同一个字符上”。代表字符集合 s , t 之间是「单射」。
* s 和 t 之间是「双射」，满足一一对应。

**`解题思路`**
* 使用哈希表 s2t , t2s 分别记录 s→t,t→s 的映射，当发现任意「一对多」的关系时返回 false 即可。

**`代码实现如下：`**
```
isIsomorphic(s, t) {
    const s2t = {};
    const t2s = {};
    for (let i = 0; i < s.length; i++) {
      const a = s[i];
      const b = t[i];
      // 对于已有映射 a -> s2t[a]，若和当前字符映射 a -> b 不匹配，
      // 说明有一对多的映射关系，则返回 false ；
      // 对于映射 b -> a 也同理
      if ((a in s2t && s2t[a] !== b) || (b in t2s && t2s[b] !== a)) {
        return false;
      }
      s2t[a] = b;
      t2s[b] = a;
    }
    return true;
  }
```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [leeCode-205 同构字符串 (哈希表解法)-初级](https://leetcode.cn/problems/isomorphic-strings/submissions/)