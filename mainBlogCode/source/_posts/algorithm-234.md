---
title: leeCode-234：回文链表-简单
date: 2023-07-11 21:51:15
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：给你一个单链表的头节点 head ，请你判断该链表是否为回文链表。如果是，返回 true ；否则，返回 false 。

**示例1:**


```
输入：head = [1,2,2,1]
输出：true

```

**示例2:**

```
输入：head = [1,2]
输出：false
```



## 题解方法一：将值复制到数组中后用双指针法

**`解题思路`**
* 所以第一步先把大写转化成小写
* 第二步就开始双指针滑动，这里写一个check函数检查这个字符是否合法，不合法就下一个，比较完了都没有return false，就代表这是个回文串。



代码实现如下： 
```
var isPalindrome = function(head) {
  let arr =[]
  while(head){
    arr.push(head.val)
    head = head.next
  }
  for(let i =0, j = arr.length-1; i < j; i++, j--){
    if(arr[i] !== arr[j]) return false
  }
  return true
}
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [力扣-回文数-简单](https://leetcode.cn/problems/palindrome-number/solutions/281686/hui-wen-shu-by-leetcode-solution/)