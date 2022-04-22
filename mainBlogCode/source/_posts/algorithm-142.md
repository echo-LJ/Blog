---
title: leeCode-142： 环形链表： (遍历、双指针两种解法)-中等
date: 2022-04-21 16:10:19
tags: 算法
---


<meta name="referrer" content="no-referrer"/>



## 题目描述： 给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。 不允许修改 链表。


**示例1:**

![截屏2022-04-19 下午1.48.40.png](https://upload-images.jianshu.io/upload_images/11846892-8cb2d705d4b16b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。

```
**示例2:**

![截屏2022-04-19 下午1.49.26.png](https://upload-images.jianshu.io/upload_images/11846892-9f90c37384264ea5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。
```

**示例3:**

![截屏2022-04-19 下午1.50.03.png](https://upload-images.jianshu.io/upload_images/11846892-c7c76142d1af2a3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
```

⚠️！！ 本题的所有解法需要对141题有一定的理解！！

## 题解方法一：遍历

`解题思路:`
* 遍历所有节点，每次遍历到一个节点时，判断该节点此前是否被访问过
* 通过哈希表存储来判断该节点是否被访问过，每次到达一个接待您，如果该节点已经存在于哈希表中，则表示该链表是环形链表，否则就将该节点存于哈希表中，重复整个过程，遍历整个链表即可。


**题目分析**


以这种解法为例
```
var detectCycle = function(head) {
    const visited = new Set();
    while (head !== null) {
        if (visited.has(head)) {
            return head;
        }
        visited.add(head);
        head = head.next;
    }
    return null;
};
```


## 题解方法二：需要参考理解题-141的第二种解法，双指针，找到相遇的节点，我真是费了好大的力气才理解了这道题，难受，智商不够！！！


`解题思路:`

![截屏2022-04-21 下午5.02.08.png](https://upload-images.jianshu.io/upload_images/11846892-d32a6d928ac28eb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
var detectCycle = function(head) {
    let fast = slow = head;
    while(fast !== null && fast.next !==null) {
        fast = fast.next.next;
        slow = slow.next;
        if (slow ===fast) {
            break
        }
    }
    if (!fast || !fast.next) {
        return null
    }
   fast = head;
    while(fast  !== slow) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow
};
```











