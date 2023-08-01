---
title: leeCode-15： 三数之和 (排序 + 双指针解法)-中等
date: 2023-07-31 16:10:10
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请你返回所有和为 0 且不重复的三元组。

⚠️： 答案中不可以包含重复的三元组。


**示例1:**


```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**示例2:**

```
输入：nums = [0,1,1]
输出：[]
解释：唯一可能的三元组和不为 0 。
```

**示例3:**

```
输入：nums = [0,0,0]
输出：[[0,0,0]]
解释：唯一可能的三元组和为 0 。
```

## 题解方法一：排序 + 双指针

**`解题思路`**
本题的难点在于如何去除重复解。

* 特判，对于数组长度 n，如果数组为 null 或者数组长度小于 333，返回 []。
* 对数组进行排序。
* 遍历排序后数组：
    1、 若 nums[i]>0：因为已经排序好，所以后面不可能有三个数加和等于 0，直接返回结果。
    2、 对于重复元素：跳过，避免出现重复解。
    3、令左指针 L=i+1，右指针 R=n−1，当 L< R 时，执行循环：
    当 nums[i]+nums[L]+nums[R]==0，执行循环，判断左界和右界是否和下一位置重复，去除重复解。并同时将 L,R移到下一位置，寻找新的解
   若和大于 0，说明 nums[R] 太大，R 左移
   若和小于 0，说明 nums[L] 太小，L 右移

**代码实现如下：** 
```
var threeSum = function(nums) {
    let len = nums.length
    if(len < 3 || nums === null) return []
    let res =  []
   nums.sort((a,b)=>a-b)
    for(let i =0; i< len; i++){
        // 若 nums[0]>0：因为已经排序好，所以后面不可能有三个数加和等于 0，直接返回[]
        if(nums[i] > 0) return res
        //  对于重复元素：跳过，避免出现重复解
        if(i > 0 && nums[i] == nums[i-1]) continue
        L = i+1
        R = len-1
        // 令左指针 L=i+1，右指针 R=n-1，当 L<R 时，执行循环：
        // 当 nums[i]+nums[L]+nums[R]==0，执行循环，判断左界和右界是否和下一位置重复，去除重复解
        while(L < R){
            let sum = nums[i] + nums[L] + nums[R]
            if( sum == 0) {
                res.push([nums[i],nums[L],nums[R]])
                while(L<R && nums[L]==nums[L+1]){
                    L=L+1
                }

                while(L<R && nums[R]==nums[R-1]){
                      R=R-1
                }
                L =L+1
                R=R-1
                        
            } else if(sum>0){
                R=R-1
            } else {
                L =L+1
            }
        }
        
    }
    return res
};

```



 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [leeCode-15： 三数之和 (两种解法)-中等](https://leetcode.cn/problems/3sum/description/)

