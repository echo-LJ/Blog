---
title: leeCode-148： 排序链表： (归并排序解法)-中等
date: 2022-04-22 09:41:57
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

## 题目描述：给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

**示例1:**


```
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```

**示例2:**
```

输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]

```

**示例3:**
```

输入：head = []
输出：[]

```

## 题解方法一：归并排序（递归法）

`通过递归实现链表归并排序，有以下两个节点：`
1、分割cut环节：找到当前链表的中点，并从中点将链表断开

* 我们使用fast、slow快慢双指针法，奇数个节点找到中点，偶数个节点找到中心左边的节点。
* 找到中点slow后，执行slow.next = null将链表切段。
* 递归分割时，输入当前链表左端点head和中心节点slow的下一个节点tmp(因为链表是从slow切断的)。
* cut递归终止条件：当head.next == None时，说明只有一个节点了，直接返回此节点。

2、合并merge环节： 将两个链表合并，转化为一个有序链表。
* 此处不过多解释，可参考提21-缺链接

![截屏2022-04-22 上午10.24.10.png](https://upload-images.jianshu.io/upload_images/11846892-b42dbe5daf321262.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现如下：
```
var sortList = function(head) {
    if(head == null || head.next ==null) {
        return head;
    }
    let fast = head.next; slow = head; // 我们使用fast、slow快慢双指针法，奇数个节点找到中点，偶数个节点找到中心左边的节点.
    while(fast & fast.next) {
        slow = slow.next;
        fast = fast.next.next
    }
    let tmp = slow.next;
    slow.next = null;
    let left = sortList(head);
    let right = sortList(tmp);
    return merge(left, right)
};


// 此处不要用merged的递归方法，会超时
var merge = (l1, l2)=>{
    let dummyNode = new ListNode(-1)
    let prev= dummyNode
    while(l1 !== null & l2 != null){
        if (l1.val<l2.val) {
            prev.next = l1
            l1 = l1.next
        } else
        {
             prev.next = l2
            l2 = l2.next
        }
        prev = prev.next
    }
    prev.next = l1 === null ? l2: l1
    return dummyNode.next
    
    
}
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
https://leetcode-cn.com/problems/sort-list/solution/sort-list-gui-bing-pai-xu-lian-biao-by-jyd/
