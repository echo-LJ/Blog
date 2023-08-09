---
title: leeCode-24： 两两交换链表中的节点： (递归、遍历)-中等
date: 2023-08-09 10:34:39
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。


**示例1:**
![截屏2023-08-09 上午10.35.44.png](https://upload-images.jianshu.io/upload_images/11846892-cee49a919d9843d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：head = [1,2,3,4]
输出：[2,1,4,3]

```

**示例2:**

```
输入：head = []
输出：[]
```

**示例3:**

```
输入：head = [1]
输出：[1]
```


## 题解方法一：遍历



**代码实现如下：**
```
var swapPairs = function(head) {
    if(!head || !head.next) return head
    let dummyNode = new ListNode(-1)
    dummyNode.next = head
    let prev = dummyNode
    while(prev.next && prev.next.next){
        let start = prev.next
        let end = prev.next.next
        prev.next = end 
        start.next = end.next
        end.next = start
        prev = start
    }
    return dummyNode.next
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
* [两两交换链表中的节点： (递归、遍历)-中等](https://leetcode.cn/problems/swap-nodes-in-pairs/submissions/)