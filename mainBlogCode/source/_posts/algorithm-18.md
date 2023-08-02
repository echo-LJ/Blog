---
title: algorithm-18： 四数之和 (排序+双指针解法)-中等
date: 2023-08-02 15:19:19
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：给你一个由 n 个整数组成的数组 nums ，和一个目标值 target 。请你找出并返回满足下述全部条件且不重复的四元组 [nums[a], nums[b], nums[c], nums[d]] （若两个四元组元素一一对应，则认为两个四元组重复）：

* 0 <= a, b, c, d < n
* a、b、c 和 d 互不相同
* nums[a] + nums[b] + nums[c] + nums[d] == target
你可以按 `任意顺序` 返回答案 。

**示例1:**


```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**示例2:**

```
输入：nums = [2,2,2,2,2], target = 8
输出：[[2,2,2,2]]
```

## 题解方法一：排序+双指针解法

**`解题思路`**

* 使用四个指针(a<b<c<d)。固定最小的a和b在左边，c=b+1,d=_size-1 移动两个指针包夹求解。
* 保存使得nums[a]+nums[b]+nums[c]+nums[d]==target的解。偏大时d左移，偏小时c右移。c和d相遇时，表示以当前的a和b为最小值的解已经全部求得。b++,进入下一轮循环b循环，当b循环结束后。a++，进入下一轮a循环。 即(a在最外层循环，里面嵌套b循环，再嵌套双指针c,d包夹求解)。


**代码实现如下：** 
```
function fourSum(nums, x) {
  if(nums.length < 4) return [];
  let res = [];
  nums.sort((a, b) => a - b);
  let n = nums.length
  for(let i = 0; i < n - 3; ++i) {
    if(i > 0 && nums[i] == nums[i - 1]) continue; // 重复直接跳出
    for(let j = i + 1; j < n - 2; ++j) {
      if(j > i + 1 && nums[j] == nums[j - 1]) continue; // 重复直接跳出
      let l = j + 1, r = n - 1;
      while(l < r) {
        let sum = 0 + nums[i] + nums[j] + nums[r] + nums[l];
        if(sum > x) r--;
        else if(sum < x) l++;
        else {
          res.push([nums[i], nums[j], nums[l], nums[r]]);
          l++, r--;
          while(l < r && nums[l] == nums[l - 1]) l++;
          while(l < r && nums[r] == nums[r + 1]) r--;
        }
      }
    }
  }
  return res;
}
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [四数之和 (排序+双指针解法)](https://leetcode.cn/problems/4sum/description/)
