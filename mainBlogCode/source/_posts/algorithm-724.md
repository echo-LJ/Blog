---
title: leeCode-724： 寻找数组的中心下标 (前缀和解法)-中等
date: 2023-08-28 10:30:52
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给你一个整数数组 nums ，请计算数组的 中心下标 。

* 数组 中心下标 是数组的一个下标，其左侧所有元素相加的和等于右侧所有元素相加的和。
* 如果中心下标位于数组最左端，那么左侧数之和视为 0 ，因为在下标的左侧不存在元素。这一点对于中心下标位于数组最右端同样适用。
* 如果数组有多个中心下标，应该返回 最靠近左边 的那一个。如果数组不存在中心下标，返回 -1 。




**示例1:**


```
输入：nums = [1, 7, 3, 6, 5, 6]
输出：3
解释：
中心下标是 3 。
左侧数之和 sum = nums[0] + nums[1] + nums[2] = 1 + 7 + 3 = 11 ，
右侧数之和 sum = nums[4] + nums[5] = 5 + 6 = 11 ，二者相等。
```

**示例2:**
```
输入：nums = [1, 2, 3]
输出：-1
解释：
数组中不存在满足此条件的中心下标。
```
**示例3:**
```
输入：nums = [2, 1, -1]
输出：0
解释：
中心下标是 0 。
左侧数之和 sum = 0 ，（下标 0 左侧不存在元素），
右侧数之和 sum = nums[1] + nums[2] = 1 + -1 = 0 。
```

### 题解方法一： 前缀和解法

**`题目分析`**
* 题目仅说明是整数数组，无其他已知条件，因此考虑直接遍历数组。
**`解题思路`**
* 设索引 i 对应变量「左侧元素相加和 sum_left 」和「右侧元素相加和 sum_right 」。
* 遍历数组 nums ，每轮更新 sum_left 和 sum_right 。
* 遍历中，遇到满足 sum_left == sum_right 时，说明当前索引为中心下标，返回即可。
* 若遍历完成，仍未找到「中心下标」，则返回 -1 。

初始化时，相当于索引 i=−1 ，此时 sum_left = 0 , sum_right = 所有元素的和 。

**代码实现如下**
```
function rotateString(s, goal) {
  return s.length === goal.length && (goal + goal).includes(s);
}
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [leeCode-724： 寻找数组的中心下标 (前缀和解法)-中等](https://leetcode.cn/problems/find-pivot-index/description/)

知识扩展：

**Array.reduce():** 方法对数组中的每个元素按序执行一个提供的 reducer 函数，每一次运行 reducer 会将先前元素的计算结果作为参数传入，最后将其结果汇总为单个返回值。

第一次执行回调函数时，不存在“上一次的计算结果”。如果需要回调函数从数组索引为 0 的元素开始执行，则需要传递初始值。否则，数组索引为 0 的元素将被用作初始值，迭代器将从第二个元素开始执行（即从索引为 1 而不是 0 的位置开始）。

```
const array1 = [1, 2, 3, 4];

// 0 + 1 + 2 + 3 + 4
const initialValue = 0;
const sumWithInitial = array1.reduce((accumulator, currentValue) => accumulator + currentValue, initialValue);

console.log(sumWithInitial);
```