---
title: algorithm-415： 字符串相加(双指针解法)-简单
date: 2023-09-07 11:04:17
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和并同样以字符串形式返回。

你不能使用任何內建的用于处理大整数的库（比如 BigInteger）， 也不能直接将输入的字符串转换为整数形式。




**示例1:**

```
输入：num1 = "456", num2 = "77"
输出："533"
```

**示例2:**

```
输入：n = 11
输出：0
解释：第 11 位数字在序列 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... 里是 0 ，它是 10 的一部分。
```
**示例3:**
```
输入：num1 = "0", num2 = "0"
输出："0"
```
## 题解方法一：双指针

**`解题思路`**
设定 i，j 两指针分别指向 num1，num2 尾部，模拟人工加法；
* 计算进位： 计算 carry = tmp // 10，代表当前位相加是否产生进位；
* 添加当前位： 计算 tmp = n1 + n2 + carry，并将当前位 tmp % 10 添加至 res 头部；
* 索引溢出处理： 当指针 i或j 走过数字首部后，给 n1，n2 赋值为 000，相当于给 num1，num2 中长度较短的数字前面填 0，以便后续计算。
* 当遍历完 num1，num2 后跳出循环，并根据 carry 值决定是否在头部添加进位 1，最终返回 res 即可。


**`代码实现如下：`** 
```

addStrings(num1, num2) {
    let res = "";
    let i = num1.length - 1;
    let j = num2.length - 1;
    let carry = 0;

    while (i >= 0 || j >= 0) {
      const n1 = i >= 0 ? parseInt(num1[i]) : 0;
      const n2 = j >= 0 ? parseInt(num2[j]) : 0;
      const tmp = n1 + n2 + carry;
      carry = Math.floor(tmp / 10);
      res = (tmp % 10) + res;
      i--;
      j--;
    }

    return (carry ? "1" : "") + res;
  }

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-415： 字符串相加-简单](https://leetcode.cn/problems/add-strings/description/)










