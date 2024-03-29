---
title: leeCode-2： 两数相加： (模拟解法)-简单
date: 2022-04-28 11:08:44
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。 请你将两个数相加，并以相同形式返回一个表示和的链表。 你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例1:**


```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```


**示例2:**

```

输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例3:**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

## 题解方法一：模拟

**`解题思路`**
* 将两个链表看成是相同长度的进行遍历，如果一个链表较短则在前面补 0，比如 987 + 23 = 987 + 023 = 1010
* 每一位计算的同时需要考虑上一位的进位问题，而当前位计算结束后同样需要更新进位值
* 如果两个链表全部遍历完毕后，进位值为 1，则在新链表最前方添加节点 1
！！小技巧：对于链表问题，返回结果为头结点时，通常需要先初始化一个预先指针 pre，该指针的下一个节点指向真正的头结点head。使用预先指针的目的在于链表初始化时无可用节点值，而且链表构造过程需要指针移动，进而会导致头指针丢失，无法返回结果。
```
var addTwoNumbers = function(l1, l2) {
   let dummyNode = new ListNode(0); // 头节点
    let cur = dummyNode; // 当前节点
    let carry = 0;
    while (l1 != null || l2 != null || carry) {
        let x = l1 == null ? 0 : l1.val;
        let y = l2 == null ? 0 : l2.val;
        let sum = x + y + carry;

        if (sum / 10 >= 1) {
            carry = 1;
        } else {
            carry = 0;
        }
        sum = sum % 10;
        cur.next = new ListNode(sum);
        cur = cur.next;
        if (l1 != null) {
            l1 = l1.next;
        }
        if (l2 != null) {
            l2 = l2.next;
        }
    }
    return dummyNode.next;
}
```

参考链接：
* [两数相加： (模拟解法)](https://leetcode.cn/problems/add-two-numbers/)
