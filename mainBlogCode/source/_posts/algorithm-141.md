---
title: leeCode-141： 环形链表：(遍历、双指针两种解法)-初级
date: 2022-04-19 13:46:46
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

跟本题无关，需要仔细阅读的文章，还没阅读https://leetcode-cn.com/problems/linked-list-cycle/solution/yi-wen-gao-ding-chang-jian-de-lian-biao-wen-ti-h-2/

## 题目描述： 给你一个链表的头节点 head ，判断链表中是否有环。如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。注意：pos 不作为参数进行传递 。仅仅是为了标识链表的实际情况。如果链表中存在环 ，则返回 true 。 否则，返回 false 。

**示例1:**

![截屏2022-04-19 下午1.48.40.png](https://upload-images.jianshu.io/upload_images/11846892-8cb2d705d4b16b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```
**示例2:**

![截屏2022-04-19 下午1.49.26.png](https://upload-images.jianshu.io/upload_images/11846892-9f90c37384264ea5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例3:**

![截屏2022-04-19 下午1.50.03.png](https://upload-images.jianshu.io/upload_images/11846892-c7c76142d1af2a3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```


## 题解方法一：遍历


`解题思路:`
* 遍历所有节点，每次遍历到一个节点时，判断该节点此前是否被访问过
* 通过哈希表存储来判断该节点是否被访问过，每次到达一个接待您，如果该节点已经存在于哈希表中，则表示该链表是环形链表，否则就将该节点存于哈希表中，重复整个过程，遍历整个链表即可。

⚠️！！同样的思路 用数组的方式遍历整个链表，显示超时，数组添加过于耗时, 可以采用ES6的 Set 数据结构。

**题目分析**

* 仔细阅读 题目中这段话`如果链表中存在环 ，则返回 true 。 否则，返回 false 。`: 此处如果只判断数组数组中是否有环，而不考虑pos的位置，则只能通过一部分测试用例。

以这种解法为例
```
var hasCycle = function(head) {
    if (head=== null||head.next == null) return false
    let arr = new Set()
    while(head) {
        if (arr.has(head.val)){
            return true
        } else {
            arr.add(head.val)
            head = head.next
        }
    }
    
    return false
};
```

* 可以通过这种没有重复val的测试用例：
```
[3,2,0,-4]
1
```
* 不可以通过这种有重复val的测试用例：14、 -21是重复的
```
[-21,10,17,8,4,26,5,35,33,-7,-16,27,-12,6,29,-12,5,9,20,14,14,2,13,-24,21,23,-21,5]
-1
```


这种方法还需要优化

## 题解方法二：


`解题思路:`
1、判断链表是否有环也可以使用双指针的方式

2、快慢指针同时指向head，快指针走两步，慢指针走一步，当快指针与慢指针相遇时，说明有环。

3、因为快指针比慢指针速度快，如果存在环，那么始终会追上。

```
var hasCycle = function(head) {
    let fast = slow = head
    while(fast && fast.next) {
        fast = fast.next.next;
        slow = slow.next;
        if (fast === slow) {
            return true
        }
    }
    return false
};
```











