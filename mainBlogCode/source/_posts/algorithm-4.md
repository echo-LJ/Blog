---
title: leeCode-4：寻找两个正序数组的中位数-困难
date: 2023-07-11 21:58:44
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数 。 算法的时间复杂度应该为 O(log (m+n)) 。

**示例1:**


```
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```

**示例2:**

```
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```



## 题解方法一：



代码实现如下： 
```var findMedianSortedArrays = function(nums1, nums2) { 
    
    let arr = []
    while(nums1.length > 0 && nums2.length > 0) {
        let ans1 = nums1[0]
        let ans2 = nums2[0]
        if(ans1 < ans2){
            arr.push(ans1)
            nums1.shift()
        } else{
            arr.push(ans2)
            nums2.shift()
        }
    }
    arr = nums1.length == 0 ? arr.concat(nums2): arr.concat(nums1)
    let length = arr.length
    if(length=== 0) return 0
    let mid = Math.floor(length / 2)
    let curr =  length %  2 === 0  ? (arr[mid-1] + arr[mid]) /2 : arr[mid]
    return curr
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [寻找两个正序数组的中位数-困难](https://leetcode.cn/problems/median-of-two-sorted-arrays/)
