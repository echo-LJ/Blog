---
title: leeCode-283： 移动零： (双指针双次循环、一次循环两种解法)-简单
date: 2022-04-29 16:22:26
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序 请注意 ，必须在不复制数组的情况下原地对数组进行操作。

**示例1:**


```
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**示例2:**

```
输入: nums = [0]
输出: [0]
```


## 题解方法一：双指针双次循环

**`解题思路`**
* 创建两个指针i， j, j指针用来记录非零的个数。
* 遍历时没遇到一个非0 的元素将其向左挪动，第一次遍历完，j指针的下标就指向了最后一个非0元素下标
* 第二次遍历的时候，起始位置就从j开始到结束，将剩下的这段区域内的元素全部置为0。

代码实现如下： 
```
var moveZeroes = function(nums) {
    if(nums==null) {
        return;
    }
    //第一次遍历的时候，j指针记录非0的个数，只要是非0的统统都赋给nums[j]
    let j = 0;
    for(let i=0;i<nums.length;++i) {
        if(nums[i]!=0) {
            nums[j++] = nums[i];
        }
    }
    //非0元素统计完了，剩下的都是0了
    //所以第二次遍历把末尾的元素都赋为0即可
    for(let i=j;i<nums.length;++i) {
        nums[i] = 0;
    }
```

## 题解方法二：双指针单次循环
* 创建两个指针i， j, j指针用来循环的前一个位置。
* nums[i]!=0，我们就交换nums[i]和nums[j]


代码实现如下： 
```
var moveZeroes = function(nums) {
    if(nums==null) {
        return;
    }
    let j = 0;
    for(let i=0;i<nums.length;++i) {
        if(nums[i]!=0) {
           var tmp = nums[i]
           nums[i] = nums[j]
           nums[j++] = tmp
        }
    }
}
```

参考链接：

* [移动零： (双指针双次循环、一次循环两种解法)](https://leetcode.cn/problems/move-zeroes/)