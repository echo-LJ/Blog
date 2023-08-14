---
title: algorithm-86： 分隔链表 （双指针解法）-中等
date: 2023-08-09 17:40:16
tags: 算法
---
<meta name="referrer" content="no-referrer"/>


## 题目描述：给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。你应当 保留 两个分区中每个节点的初始相对位置。


**示例1:**
![截屏2023-08-14 下午4.46.24.png](https://upload-images.jianshu.io/upload_images/11846892-ec75a36739413c23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
![截屏2023-08-14 下午4.46.24.png](https://upload-images.jianshu.io/upload_images/11846892-ec75a36739413c23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```

**示例2:**
```
输入：head = [2,1], x = 2
输出：[1,2]
```



## 题解方法一：双指针

**`解题思路:`**

* 新建链表：sml_dummy`存储节点值<x的值`, big_dummy`存储节点值 ≥x`的值。
* 遍历链表head 并比较head.val与x的大小;
    1、若 head.val < x ，则将节点 head 添加至链表 sml_dummy 最后面；
    2、若 head.val >= x ，则将节点 head 添加至链表 big_dummy 最后面；
* 遍历完成后，拼接 sml_dummy 和 big_dummy 链表。
* 最终返回头节点 sml_dummy.next 即可。

**`代码实现如下`**
```
var partition = function(head, x) {
    let sml_dummy = new ListNode(0)
    let big_dummy = new ListNode(0)
    let sml = sml_dummy
    let big = big_dummy
    while(head){
        if(head.val < x){
            sml.next = head
            sml = sml.next
        } else {
            big.next =head
            big = big.next
        }
        head = head.next
    }
    sml.next = big_dummy.next
    big.next = null
    return sml_dummy.next
};
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-86： 分隔链表 （双指针解法）-中等](https://leetcode.cn/problems/partition-list/submissions/456318287/)


