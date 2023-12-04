---
title: algorithm-JZ60  n个骰子的点数-中等
date: 2023-09-14 15:36:31
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述： 把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。你需要用一个浮点数数组返回答案，其中第 i 个元素代表这 n 个骰子所能掷出的点数集合中第 i 小的那个的概率。


**示例1:**


```
输入: 1
输出: [0.16667,0.16667,0.16667,0.16667,0.16667,0.16667]
```
**示例2:**


```
输入: 2
输出: [0.02778,0.05556,0.08333,0.11111,0.13889,0.16667,0.13889,0.11111,0.08333,0.05556,0.02778]
```

## 题解方法一： 动态规划
* 设输入 n 个骰子的解（即概率列表）为 f(n) ，其中「点数和」 x 的概率为 f(n,x) 。
* 假设 n-1个的解为 f(n-1),此时添加一枚骰子，求 n 个骰子的点数和为 x 的概率 f(n,x) 。
* 当添加骰子的点数为 1 时，前 n−1 个骰子的点数和应为 x−1 ，方可组成点数和 x. 同理，以此类推，直至此骰子点数为 6。 将这 6 种情况的概率相加，即可得到概率 f(n,x) 。

![截屏2023-09-14 下午3.48.01.png](https://upload-images.jianshu.io/upload_images/11846892-9b0944ea3e9b9c1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 根据以上分析，得知通过子问题的解 f(n−1)可递推计算出 f(n) ，而输入一个骰子的解 f(1) 已知，因此可通过解 f(1) 依次递推出任意解 f(n)。
* 注意越界问题，例如，若希望递推计算 f(2,2)，由于一个骰子的点数和范围为 [1,6] ，因此只应求和 f(1,1)，即 f(1,0), f(1,−1), ... , f(1,−4) 皆无意义。此越界问题导致代码编写的难度提升。
![截屏2023-09-14 下午3.58.55.png](https://upload-images.jianshu.io/upload_images/11846892-07146054594a8085.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 具体来看，由于新增骰子的点数只可能为 1 至 6 ，因此概率 f(n−1,x) 仅与 f(n,x+1) , f(n,x+2), ... , f(n,x+6) 相关。因而，遍历 f(n−1) 中各点数和的概率，并将其相加至 f(n) 中所有相关项，即可完成 f(n−1) 至 f(n) 的递推。


* 
**`代码实现如下：`** 
```

function dicesProbability(n) {
  let dp = new Array(6).fill(1 / 6);
  for (let i = 2; i <= n; i++) {
    let tmp = new Array(5 * i + 1).fill(0);
    for (let j = 0; j < dp.length; j++) {
      for (let k = 0; k < 6; k++) {
        tmp[j + k] += dp[j] / 6;
      }
    }
    dp = tmp;
  }
  return dp;
}

```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-JZ60  n个骰子的点数-中等](https://leetcode.cn/problems/nge-tou-zi-de-dian-shu-lcof/description/)




