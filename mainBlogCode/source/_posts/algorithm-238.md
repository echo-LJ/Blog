---
title: algorithm-238: 除自身以外数组的乘积(上三角-下三角)-中等
date: 2023-09-06 11:03:43
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

## 题目描述：给你一个整数数组 nums，返回 数组 answer ，其中 answer[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积 。

题目数据 保证 数组 nums之中任意元素的全部前缀元素和后缀的乘积都在  32 位 整数范围内。

请不要使用除法，且在 O(n) 时间复杂度内完成此题。

**示例1:**


```

输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**示例2:**
```
输入: nums = [-1,1,0,-3,3]
输出: [0,0,9,0,0]
```

### 题解方法一： 

**`解题思路`**

[参考](https://leetcode.cn/problems/product-of-array-except-self/solutions/11472/product-of-array-except-self-shang-san-jiao-xia-sa/)


**代码实现如下**
```
var  productExceptSelf = (nums) => {
    const ans = new Array(nums.length).fill(1);
    let tmp = 1;

    for (let i = 1; i < nums.length; i++) {
      ans[i] = ans[i - 1] * nums[i - 1]; // 下三角
    }

    for (let i = nums.length - 2; i >= 0; i--) {
      tmp *= nums[i + 1];                // 上三角
      ans[i] *= tmp;                     // 下三角 * 上三角
    }

    return ans;
  }
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [除自身以外数组的乘积-中等](https://leetcode.cn/problems/product-of-array-except-self/description/)