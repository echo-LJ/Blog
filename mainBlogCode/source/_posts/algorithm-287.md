---
title: leeCode-287： 寻找重复数： (哈希表解法)-中等
date: 2023-09-01 15:14:47
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定一个包含 n + 1 个整数的数组 nums ，其数字都在 [1, n] 范围内（包括 1 和 n），可知至少存在一个重复的整数。假设 nums 只有 一个重复的整数 ，返回 这个重复的数 。你设计的解决方案必须 不修改 数组 nums 且只用常量级 O(1) 的额外空间。

**示例1:**
```
输入：nums = [1,3,4,2,2]
输出：2
```

**示例2:**

```
输入：nums = [3,1,3,4,2]
输出：3
```


## 题解方法一：哈希表

**`解题思路`**
* 创建哈希表hmap,遍历存入hmap,如果存在，直接返回
* 遍历结束，返回-1
**`代码实现如下：`** 
```
function findDuplicate(nums) {
    let set = new Set();
    for (let num of nums) {
        if (set.has(num)) return num;
        set.add(num);
    }
    return -1;
}
```

## 题解方法二：原地交换
**`解题思路`**
* 根据题意： 在一个长度为 n 的数组 nums 里的所有数字都在 0 ~ n-1 的范围内。则数组元素的 索引 和 值 是 一对多 的关系
* 可遍历数组并通过交换操作，使元素的 索引 与 值 一一对应 nums[i] = i
* 因而，就能通过索引映射对应的值，起到与字典等价的作用。

**`算法流程`**
* 遍历数组 nums，设索引初始值为 i=0:
    - 若 nums[i]=i ： 说明此数字已在对应索引位置，无需交换，因此跳过。
    - 若 nums[nums[i]]=nums[i] ： 代表索引 nums[i] 处和索引 i 处的元素值都为 nums[i]，即找到一组重复值，返回此值 nums[i] 。
    - 否则： 交换索引为 i 和 nums[i] 的元素值，将此数字交换至对应索引位置。
* 若遍历完毕尚未返回，则返回 −1。


参考链接：

* [ leeCode-287： 寻找重复数： (哈希表解法)-中等](https://leetcode.cn/problems/find-the-duplicate-number/description/)