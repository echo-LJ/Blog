---
title: leeCode-26： 删除有序数组中的重复项（双指针）-中等
date: 2023-08-09 11:05:48
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。考虑 nums 的唯一元素的数量为 k ，你需要做以下事情确保你的题解可以被通过：更改数组 nums ，使 nums 的前 k 个元素包含唯一元素，并按照它们最初在 nums 中出现的顺序排列。nums 的其余元素与 nums 的大小不重要。返回 k 。

**示例1:**
```
输入：nums = [1,1,2]
输出：2, nums = [1,2,_]
解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。
```

**示例2:**

```
输入：nums = [0,0,1,1,1,2,2,3,3,4]
输出：5, nums = [0,1,2,3,4]
解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。
```

## 题解方法一：双指针

`解题思路:`
* 1、定义快指针和慢指针 fast、slow，快指针表示遍历数组到达的下标位置，慢指针表示下一个不同元素要填入的下标位置，初始时两个指针指向下标 fast = 1, slow = 0。

* 2、假设数组 nums 的长度为 n。将快指针 fast 依次遍历从 1 到 n−1 的每个位置，对于每个位置，如果 nums[slow] !== nums[fast]说明 nums[fast] 和之前的元素都不同，因此将 nums[fast]的值复制到 nums[slow+1] ，然后将 slow的值加 1，即指向下一个位置。

遍历结束之后，从 nums[0] 到 nums[slow−1] 的每个元素都不相同且包含原数组中的每个不同的元素，因此新的长度即为 slow，返回 slow 即可。


**代码实现如下：**
```
var removeDuplicates = function(nums) {
    if(nums == null || nums.length == 0) return 0;
    let slow = 0;
    let fast = 1
    let n = nums.length
    while(fast< n){
        if(nums[slow] !== nums[fast]){
            nums[slow+1] = nums[fast]
            slow++
        }
        fast++
    }
    return  slow+1
};

```

## 题解方法二：递归

**代码实现如下：**
![截屏2023-08-09 上午10.59.26.png](https://upload-images.jianshu.io/upload_images/11846892-5a765f394b194d11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```

var swapPairs = function(head) {
    if(!head || !head.next) return head
    let next = head.next
    head.next = swapPairs(next.next)
    next.next = head
    return next
};
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [leeCode-26： 删除有序数组中的重复项（双指针）-中等](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)