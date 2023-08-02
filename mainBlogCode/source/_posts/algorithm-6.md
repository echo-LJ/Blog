---
title: leeCode-6： N 字形变换-中等
date: 2023-08-01 14:56:03
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  将一个给定字符串 s 根据给定的行数 numRows ，以从上往下、从左到右进行 Z 字形排列。比如输入字符串为 "PAYPALISHIRING" 行数为 3 时，排列如下：
```
P   A   H   N
A P L S I I G
Y   I   R
```
之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："PAHNAPLSIIGYIR"。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```


**示例1:**


```
输入：s = "PAYPALISHIRING", numRows = 3
输出："PAHNAPLSIIGYIR"
```

**示例2:**

```
输入：s = "PAYPALISHIRING", numRows = 4
输出："PINALSIGYAHRPI"
解释：
P     I    N
A   L S  I G
Y A   H R
P     I
```

**示例3:**

```
输入：s = "A", numRows = 1
输出："A"
```


## 题解方法一：

**`解题思路`**
* 模拟这个行索引的变化，在遍历 s 中把每个字符填到正确的行 res[i]中。


**代码实现如下：**
```
var convert = function(s, numRows) {
    if (numRows < 2) return s; // 如果1行展示直接放回原字符串
    const res = new Array(numRows).fill(""); // 长度为numRows的数组
    let i = 0, flag = -1; 
    for (let c of s) { // 遍历字符串
      res[i] += c; // 存入数组中
      if (i === 0 || i === numRows - 1) flag = -flag; // i=0 第1行，i === numRows - 1 最后一行进行翻转，实现函数递增和递减
      i += flag;
    }
    return res.join("");
};

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [ N 字形变换-中等](https://leetcode.cn/problems/zigzag-conversion/)