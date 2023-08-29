---
title: leeCode-876： 链表的中间结点(双指针解法)-简单
date: 2023-08-29 11:09:57
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述：给你单链表的头结点 head ，请你找出并返回链表的中间结点。如果有两个中间结点，则返回第二个中间结点。

**示例1:**


```
输入：head = [1,2,3,4,5]
输出：[3,4,5]
解释：链表只有一个中间结点，值为 3 。
```

**示例2:**

```
输入：head = [1,2,3,4,5,6]
输出：[4,5,6]
解释：该链表有两个中间结点，值分别为 3 和 4 ，返回第二个结点。
```



## 题解方法一：快慢双指针


代码实现如下： 
```
function middleNode(head) {
  let fast = head;
  let slow = head;
  while (fast && fast.next) {
    fast = fast.next.next;
    slow = slow.next;
  }
  return slow;
}
```


## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [链表的中间结点(双指针解法)-简单](https://leetcode.cn/problems/middle-of-the-linked-list/solutions/)