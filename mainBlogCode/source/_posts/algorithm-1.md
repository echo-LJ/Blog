---
title: leeCode-1： 两数之和 (暴力循环、哈希两种解法)-简单
date: 2022-04-27 14:43:32
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出和为目标值 target  的那 两个 整数，并返回它们的数组下标。你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。你可以按任意顺序返回答案。

**示例1:**


```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例2:**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例3:**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

## 题解方法一：暴力枚举:

**`解题思路`**
* 最容易想到的方法是枚举数组中的每一个数 x，寻找数组中是否存在 target - x。

* 当我们使用遍历整个数组的方式寻找 target - x 时，需要注意到每一个位于 x 之前的元素都已经和 x 匹配过，因此不需要再进行匹配。而每一个元素不能被使用两次，所以我们只需要在 x 后面的元素中寻找 target - x。

代码实现如下： 
```
var twoSum = function(nums, target) {
    let len = nums.length
    for (let i = 0; i<len; i++) {
        for (let j = i +1; j < len; j++) {
            if (nums[i]+nums[j] === target) {
                return [i,j]
            }
        }
    }
};
```


## 题解方法二：哈希

注意到方法一的时间复杂度较高的原因是寻找 target - x 的时间复杂度过高。因此，我们需要一种更优秀的方法，能够快速寻找数组中是否存在目标元素。如果存在，我们需要找出它的索引。

**`解题思路`**

* 

使用哈希表，可以将寻找 target - x 的时间复杂度降低到从 O(N)O(N) 降低到 O(1)O(1)。

这样我们创建一个哈希表，对于每一个 x，我们首先查询哈希表中是否存在 target - x，然后将 x 插入到哈希表中，即可保证不会让 x 和自己匹配。


代码实现如下：

```
var twoSum = function(nums, target) {
    let res = new Map()
    let len = nums.length
    for(let i = 0; i< len; i++) {
        if(res.has(target-nums[i])) return [i, res.get(target-nums[i])]
        res.set(nums[i],i)
    }
}
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [两数之和](https://leetcode.cn/problems/two-sum/)

