---
title: leeCode-796：旋转字符串-简单
date: 2023-08-09 17:41:41
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定两个字符串, s 和 goal。如果在若干次旋转操作之后，s 能变成 goal ，那么返回 true 。s 的 旋转操作 就是将 s 最左边的字符移动到最右边。 例如, 若 s = 'abcde'，在旋转一次之后结果就是'bcdea' 。。



**示例1:**


```
输入: s = "abcde", goal = "cdeab"
输出: true
```

**示例2:**
```
输入: s = "abcde", goal = "abced"
输出: false
```

### 题解方法一： 深度优先搜索DFS

**`解题思路`**
输入一个字符串 sss ，做如下操作：

* 选择任意位置，将字符串切分为两个子字符串 s=L R、
* 将 R 移动至 L 前面得到 goal=R L

此时，称 goal  为 s 的一个「旋转字符串」。
根据旋转字符串特点，若构造一个拼接字符串 goalgoal ，则有 goalgoal=RLRL=RsL，即拼接字符串  goalgoal 中包含原字符串 s。

* 字符串 s , goal 的长度相等；
* 拼接字符串 goal  中包含原字符串 s ；


** 代码实现如下：**
```
function rotateString(s, goal) {
  return s.length === goal.length && (goal + goal).includes(s);
}
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [旋转字符串-简单](https://leetcode.cn/problems/rotate-string/description/)