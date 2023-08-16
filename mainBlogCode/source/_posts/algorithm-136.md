---
title: leeCode-136： 只出现一次的数字（位运算）-简单
date: 2023-08-16 15:47:22
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

跟本题无关，需要仔细阅读的文章，还没阅读https://leetcode-cn.com/problems/linked-list-cycle/solution/yi-wen-gao-ding-chang-jian-de-lian-biao-wen-ti-h-2/

## 题目描述： 给你一个 非空 整数数组 nums ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。



**示例1:**


```
输入：nums = [2,2,1]
输出：1
```
**示例2:**

```
输入：nums = [4,1,2,1,2]
输出：4
```

**示例3:**

```
输入：nums = [1]
输出：1
```


## 题解方法一：异或运算


**`解题思路:`**
* 异或运算有个重要的性质，两个相同数字异或为 000 ，即对于任意整数 aaa 有 a⊕a=0  。因此，若将 nums  中所有数字执行异或运算，留下的结果则为 出现一次的数字 x。


`举例`： 5+3 
```
  101    (x 的二进制表示)
^ 011    (num 的二进制表示)
------
  110    (异或的结果)
```

**`代码实现如下`**
```
function singleNumber(nums) {
  let x = 0;
  for (let num of nums) {  // 1. 遍历 nums 执行异或运算
    x ^= num;      
  }
  return x;         // 2. 返回出现一次的数字 x
}
```

参考链接:

* [ leeCode-136： 只出现一次的数字（位运算）-简单](https://leetcode.cn/problems/single-number/solutions/2361995/136-zhi-chu-xian-yi-ci-de-shu-zi-wei-yun-iyd0/)












