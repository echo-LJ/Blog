---
title: leeCode-283： 第一个错误的版本： (二分查找解法)-简单
date: 2023-08-26 20:15:46
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。



**示例1:**


```
输入：n = 5, bad = 4
输出：4
解释：
调用 isBadVersion(3) -> false 
调用 isBadVersion(5) -> true 
调用 isBadVersion(4) -> true
所以，4 是第一个错误的版本。
```

**示例2:**

```
输入：n = 1, bad = 1
输出：1
```


## 题解方法一：二分查找

**`解题思路`**
* 根据题意： 错误的版本之后的所有版本都是错的， 那么正确的版本之前都是正确版本。
* 以某个版本为分界点， 左侧如果是正确的，那么所有都是左侧所有都是正确的
* 右侧如果是错误的，那么右侧所有的都是错误的。
* 采用二分查找，缩短查找空间。

**`代码实现如下：`** 
```
var solution = function(isBadVersion) {
    /**
     * @param {integer} n Total versions
     * @return {integer} The first bad version
     */
    return function(n) {
        let i = 1, j = n;
      while (i <= j) {
        // 向下取整除法计算中点 m 
        let m = Math.floor((i + j) / 2);
        // 若 m 是错误版本，则最后一个正确版本一定在闭区间 [i, m - 1]
        if (isBadVersion(m)) j = m - 1;
        // 若 m 是正确版本，则首个错误版本一定在闭区间 [m + 1, j]
        else i = m + 1;
      }
    // i 指向首个错误版本，j 指向最后一个正确版本
    return i;
    };
};
```


参考链接：

* [leeCode-283： 第一个错误的版本： (二分查找解法)-简单]https://leetcode.cn/problems/first-bad-version/solutions/1693158/by-jyd-19pr/