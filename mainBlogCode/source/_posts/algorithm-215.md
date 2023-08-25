---
title: leeCode-215：数组中的第K个最大元素-中等
date: 2023-08-23 14:18:29
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。


**示例1:**



```
输入: [3,2,1,5,6,4], k = 2
输出: 5
```

**示例2:**

```

输入: [3,2,3,1,2,4,5,5,6], k = 4
输出: 4

```


### 题解方法一： 暴力查找


**`代码实现如下：`**
```
var findKthLargest = function(nums, k) {
    nums.sort((a,b)=>a-b)
    let len = nums.length
    if(len < k) return nulll
    return nums[len-k]
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [leeCode-215：数组中的第K个最大元素）](https://leetcode.cn/problems/kth-largest-element-in-an-array/solutions/2361969/215-shu-zu-zhong-de-di-k-ge-zui-da-yuan-d786p/)
