---
title: algorithm-400： 第 N 位数字-中等
date: 2023-08-16 13:38:08
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给你一个整数 n ，请你在无限的整数序列 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...] 中找出并返回第 n 位上的数字。 


**示例1:**

```
输入：n = 3
输出：3
```

**示例2:**

```
输入：n = 11
输出：0
解释：第 11 位数字在序列 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... 里是 0 ，它是 10 的一部分。
```

## 题解方法一：

**`解题思路`**
参考： [Krahets](https://leetcode.cn/problems/nth-digit/solutions/2362054/400-di-n-wei-shu-zi-qing-xi-tu-jie-by-jy-sz5y/)
**`代码实现如下：`** 
```

function findNthDigit(n) {
  let digit = 1; // 位数
  let start = 1; // 位数其实数字 1 10 100
  let count = 9; // 位数数量
 // 确定所求数位的所在数字的位数
  while (n > count) { // 1.
    n -= count;
    start *= 10;
    digit += 1;
    count = 9 * start * digit;
  }
    // 确定所求数位所在的数字
  let num = start + Math.floor((n - 1) / digit); // 2.
  console.log(num)
  // 所求数位在 numnumnum 的哪一数位
  // 获得 num 的 第 (n - 1) % digit 个数位，并转化为 int
  return Number(String(num)[(n - 1) % digit]); // 3.
}


```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-400： 第 N 位数字-中等](https://leetcode.cn/problems/house-robber-iii/)










