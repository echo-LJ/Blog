---
title: leeCode-96： 不同的二叉搜索树：(动态规划、数学-卡塔兰数)-中等
date: 2022-06-06 14:20:58
tags:
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给你一个整数 n ，求恰由 n 个节点组成且节点值从 1 到 n 互不相同的 二叉搜索树 有多少种？返回满足题意的二叉搜索树的种数。

**`题目分析：我们可以看到二叉树的规律是左节点<根节点< 右节点`**

**示例1:**
![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-4f203d137c8782e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：n = 3
输出：5
```
**示例2:**

```
输入：n = 1
输出：1
```



### 题解方法一： 动态规划

**`题目思路：给定一个有序序列 1⋯n，为了构建出一棵二叉搜索树，我们可以遍历每个数字i，将该数字作为树根，将 1⋯(i−1) 序列作为左子树，将 (i+1)⋯n 序列作为右子树。接着我们可以按照同样的方式递归构建左子树和右子树。在上述构建的过程中，由于根的值不同，因此我们能保证每棵二叉搜索树是唯一的。由此可见，原问题可以分解成规模较小的两个子问题，且子问题的解可以复用。因此，我们可以想到使用动态规划来求解本题`**

**`题目分析：题目要求是计算不同二叉搜索树的个数。为此，我们可以定义两个函数：`**

* 1、G(n): 长度为 n 的序列能构成的不同二叉搜索树的个数。

* 2、F(i, n): 以 i 为根、序列长度为 n 的不同二叉搜索树个数 （1<= i <= n）

可见，G(n)是我们求解需要的函数。

首先，根据解题的思路，不同的二叉搜索树的总数 G(n)，是对遍历所有 i(1≤i≤n) 的 F(i, n)F(i,n) 之和。换言之：

![ans1.png](https://upload-images.jianshu.io/upload_images/11846892-c5b48f12d85d1ffb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于边界情况，当序列长度为 1（只有根）或为 0（空树）时，只有一种情况，即：
```
G(0)=1,G(1)=1
```
给定序列 1⋯n，我们选择数字 i 作为根，则根为 i 的所有二叉搜索树的集合是左子树集合和右子树集合的笛卡尔积，对于笛卡尔积中的每个元素，加上根节点之后形成完整的二叉搜索树，如下图所示：

![ans2.png](https://upload-images.jianshu.io/upload_images/11846892-27c9a147947eaf27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`举例分析`**

创建以3为根、长度为7 的不同二叉搜索树整个序列是 [1, 2, 3, 4, 5, 6, 7]，我们需要从左子序列 [1, 2] 构建左子树，从右子序列 [4, 5, 6, 7] 构建右子树，然后将它们组合（即笛卡尔积）。

对于这个例子，不同的二叉搜索树的个数为F(3,7)。我们将 [1,2] 构建不同左子树的数量表示为 G(2), 从 [4, 5, 6, 7] 构建不同右子树的数量表示为 G(4)，注意到 G(n) 和序列的内容无关，只和序列的长度有关。于是F(3,7)=G(2)⋅G(4)。 因此，我们可以得到以下公式：
![ans3.png](https://upload-images.jianshu.io/upload_images/11846892-951ad8ad3f357934.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将公式 (1)，(2) 结合，可以得到 G(n) 的递归表达式：
![ans4.png](https://upload-images.jianshu.io/upload_images/11846892-82da2a211924920b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此，我们从小到大计算 G 函数即可，因为 G(n) 的值依赖于 G(0)⋯G(n−1)。

代码实现如下：

```
var numTrees = function(n) {
    const G = new Array(n + 1).fill(0);
    G[0] = 1;
    G[1] = 1;

    for (let i = 2; i <= n; ++i) {
        for (let j = 1; j <= i; ++j) {
            G[i] += G[j - 1] * G[i - j];
        }
    }
    return G[n];
};
```

### 题解方法二： 数学-卡塔兰数

**`题目思路：数学`**
事实上我们在方法一中推导出的 G(n)G(n)函数的值在数学上被称为卡塔兰数 Cn。卡塔兰数更便于计算的定义如下:

![ans5.png](https://upload-images.jianshu.io/upload_images/11846892-eb444c7c6f90152b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现如下：

```
var numTrees = function(n) {
    let C = 1;
    for(let i =0; i< n; ++i) {
        C = C * 2 * (2 * i + 1) / (i + 2);
    }
    return C;
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/unique-binary-search-trees/solution/bu-tong-de-er-cha-sou-suo-shu-by-leetcode-solution/


