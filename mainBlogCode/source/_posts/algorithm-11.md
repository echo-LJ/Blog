---
title: leeCode-11： 盛水最多的容器(双指针解法)-中等
date: 2023-08-02 10:41:32
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。

**示例1:**

![question_11.jpeg](https://upload-images.jianshu.io/upload_images/11846892-87bf000592698917.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例2:**

```
输入：height = [1,1]
输出：1
```


## 题解方法一： 双指针

**`解题思路`**
* 设两指针 i , j ，指向的水槽板高度分别为 h[i]h, h[j]，此状态下水槽面积为 S(i,j)。由于可容纳水的高度由两板中的 `短板` 决定，因此可得如下 面积公式 ：
```
S(i,j) = Math.min( h[i]h, h[j]) * (j-i)
```
![1628780627-VtSmcP-Picture0.png](https://upload-images.jianshu.io/upload_images/11846892-a845eb7b4b65357a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在每个状态下，无论长板或短板向中间收窄一格，都会导致水槽 底边宽度 −1-1−1​ 变短：
* 若向内 移动短板 ，水槽的短板 min(h[i],h[j]) 可能变大，因此下个水槽的面积 可能增大 。
* 若向内 移动长板 ，水槽的短板 min(h[i],h[j]) 不变或变小，因此下个水槽的面积 一定变小 。
因此，初始化双指针分列水槽左右两端，循环每轮将短板向内移动一格，并更新面积最大值，直到两指针相遇时跳出；即可获得最大面积。
**`算法流程`**
* 初始化： 双指针 i , j 分列水槽左右两端；
* 循环收窄： 直至双指针相遇时跳出；
    1、更新面积最大值 res ；
    2、选定两板高度中的短板，向中间收窄一格；
* 返回值： 返回面积最大值 res即可；

    
**代码实现如下：**
```
var maxArea = function(height) {
    let i = 0
    let j = height.length -1
    let res = 0
    while(i<j){
        if(height[i] < height[j]){
            res = Math.max(res, height[i] * (j-i))
            i++
        } else {
            res = Math.max(res, height[j] * (j-i))
            j--
        }
    }
    return res
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [盛水最多的容器(双指针解法)-中等](https://leetcode.cn/problems/container-with-most-water/)
* [Krahets神解法](https://leetcode.cn/problems/container-with-most-water/solutions/11491/container-with-most-water-shuang-zhi-zhen-fa-yi-do/)

 