---
title: leeCode-169：多数元素(哈希查找、排序、摩尔投票解法)-简单
date: 2023-08-16 15:08:54
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例1:**

```
输入：nums = [3,2,3]
输出：3
```

**示例2:**
```
输入：nums = [2,2,1,1,1,2,2]
输出：2
```

## 题解方法一：哈希查找
**`代码实现如下：`**
```
function majorityElement(nums) {
  let counts = new Map();
  for (let num of nums) {
    if (counts.has(num)) {
      counts.set(num, counts.get(num) + 1);
    } else {
      counts.set(num, 1);
    }
  }
  let maxCount = 0;
  let majorityNum;
  for (let [num, count] of counts) {
    if (count > maxCount) {
      maxCount = count;
      majorityNum = num;
    }
  }
  return majorityNum;
}
```
## 题解方法二：排序
**`代码实现如下：`**
```
function majorityElement(nums) {
  nums.sort((a, b) => a - b);
  return nums[Math.floor(nums.length / 2)];
}
```
## 题解方法三：摩尔投票

核心理念为 票数正负抵消 。此方法时间和空间复杂度分别为 O(N) 和 O(1) ，为本题的最佳解法。

**`解题思路`**
* 设数组长度为n,输入数组 nums 的众数为 x;
* 推论一：若记众数的票数为+1,非众数的票数为-1， 则一定所有数字的票数和>0 (因为众数的次数为> n/2)
* 推论二： 若数组的前 a 个数字的 票数和 =0  (多次为0，最终总是众数多)，则 数组剩余 (n−a) 个数字的 票数和一定仍 >0 ，即后 (n−a)个数字的 众数仍为 x 。

**`代码实现如下：`**
```
function majorityElement(nums) {
  let votes = 0;
  let x;
  for (let num of nums) {
    if (votes === 0) { //投票合为0的时候，众数为当前值
      x = num;
    }
    votes += num === x ? 1 : -1;
  }
  return x;
}
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [leeCode-169：多数元素(哈希查找、排序、摩尔投票解法)-简单](https://leetcode.cn/problems/majority-element/description/)
