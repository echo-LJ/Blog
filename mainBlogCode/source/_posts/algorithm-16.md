---
title: leeCode-16： 最接近的三数之和 (排序 + 双指针解法)-中等
date: 2023-08-02 13:45:26
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你一个长度为 n 的整数数组 nums 和 一个目标值 target。请你从 nums 中选出三个整数，使它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在恰好一个解。



**示例1:**


```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

**示例2:**

```
输入：nums = [0,0,0], target = 1
输出：0
```

``

## 题解方法一：排序 + 双指针

**`解题思路`**
本题的难点在于如何去除重复解。

设`s = nums[i] + nums[j] + nums[k]` 为了判断s与target是不是最近的数，通过变量mindiff来维护｜s-target｜最小值。
* 如果 `s= target`, 则直接返回s。
* 如果 `s>targets`，那么如果 `s−target<minDiff`，说明找到了一个与 target 更近的数，更新 minDiff为 s−target更新答案为 s。然后和三数之和一样，把 k 减一。
* 如果 `s《targets`，那么如果 `target-s<minDiff`，说明找到了一个与 target 更近的数，更新 minDiff为 target-s更新答案为 s。然后和三数之和一样，把 j 加一。

**二次优化**
* 设`s = nums[i] + nums[i+1] + nums[i+2]`。如果 s>target，由于数组已经排序，后面无论怎么选，选出的三个数的和不会比 s 还小，所以不会找到比 s 更优的答案了。所以只要 s>target，就可以直接 break 外层循环了。在 break 前判断 s 是否离 target 更近，如果更近，那么更新答案为 s。
* 设`s = nums[i] + nums[n-2] + nums[n-1]`。如果 `s<target`，由于数组已经排序，nums[i]加上后面任意两个数都不超过 s，所以下面的双指针就不需要跑了，无法找到比 s 更优的答案。但是后面还有更大的 nums[i]，可能找到一个离 target 更近的三数之和，所以还需要继续枚举，continue 外层循环。在 continue 前判断 s 是否离 target 更近，如果更近，那么更新答案为 s，更新 minDiff 为 target−s。
* 如果 i>0 且 nums[i]=nums[i−1]，那么 nums[i] 和后面数字相加的结果，必然在之前算出过，所以无需跑下面的双指针，直接 continue 外层循环。（可以放在循环开头判断。）

**代码实现如下：** 
```
var threeSumClosest = function(nums, target) {
    let n = nums.length
    let res =  0
    let minDiff = Number.MAX_SAFE_INTEGER;
    nums.sort((a,b)=>a-b)
    for(let i =0; i< n-2; i++){
        const x = nums[i]
         if (i > 0 && x === nums[i - 1]) {
            continue; // 优化三
        }
        // 优化一
        let s = x + nums[i + 1] + nums[i + 2];
        if(s > target){// 后面无论怎么选，选出的三个数的和不会比 s 还小
            if (s - target < minDiff) {
                ans = s; // 由于下面直接 break，这里无需更新 minDiff
            }
            break;
        }

        // 优化二
        s = x + nums[n - 2] + nums[n - 1];
        if (s < target) { // x 加上后面任意两个数都不超过 s，所以下面的双指针就不需要跑了
            if (target - s < minDiff) {
                minDiff = target - s;
                ans = s;
            }
            continue;
        }

        let j = i+1, k = n-1
        while(j < k){
            s = x + nums[j] + nums[k];
            if (s === target) {
                return target;
            }
            if (s > target) {
                if (s - target < minDiff) { // s 与 target 更近
                    minDiff = s - target;
                    ans = s;
                }
                k--;
            } else { // s < target
                if (target - s < minDiff) { // s 与 target 更近
                    minDiff = target - s;
                    ans = s;
                }
                j++
            }
        }
        
    }
    return ans
};

```



 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [leeCode-15： 三数之和 (两种解法)-中等](https://leetcode.cn/problems/3sum/description/)

