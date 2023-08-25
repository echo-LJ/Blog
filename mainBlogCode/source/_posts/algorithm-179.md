---
title: leeCode-179： 最大数(贪心算法)-简单
date: 2023-08-23 14:29:59
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定一组非负整数 nums，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。

注意：输出结果可能非常大，所以你需要返回一个字符串而不是整数。
**示例1:**

```
输入：nums = [10,2]
输出："210"
```

**示例2:**
```
输入：nums = [3,30,34,5,9]
输出："9534330"
```

## 题解方法一：逐位查找

**`算法思路`**
1、设数组 numsnumsnums 中任意两数字的字符串为 xxx 和 yyy ，则规定 贪心策略。
* 若拼接字符串 x+y>y+x ，则 x “大于” y 。
* 反之，若 x+y<y+x ，则 x “小于” y 。
x “小于” y 代表：排序完成后，数组中 x 应在 y 左边；“大于” 则反之。
算法流程：
初始化： 字符串列表 strs  ，保存各数字的字符串格式。
列表排序： 根据贪心策略对 strs  进行从大到小排序。
返回值： 拼接 strs  中的所有字符串，并返回。


**`代码实现如下：`**
```
function majorityElement(nums) {
  let res = 0;
  while (n) {
    res += n & 1;
    n >>= 1;
  }
  return res;
}
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [位1的个数(逐位查找)-简单](https://leetcode.cn/problems/number-of-1-bits/solutions/2361978/191-wei-1-de-ge-shu-wei-yun-suan-qing-xi-40rw/)

