---
title: leeCode-965： 单值二叉树： (深度优先搜索解法)-简单
date: 2022-08-01 13:46:58
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  如果二叉树每个节点都具有相同的值，那么该二叉树就是单值二叉树,只有给定的树是单值二叉树时，才返回 true；否则返回 false。。

**示例1:**


```
输入：[1,1,1,1,1,null,1]
输出：true
```
![ex1.png](https://upload-images.jianshu.io/upload_images/11846892-17164e4b97d6ed6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**示例2:**

```
输入：[2,2,2,5,2]
输出：false
```
![ex2.png](https://upload-images.jianshu.io/upload_images/11846892-e02638c33ca66b2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 题解方法一：深度优先搜索

**`解题思路`**

* 一棵树的所有节点都有相同的值，当且仅当对于树上的每一条边的两个端点，它们都有相同的值（这样根据传递性，所有节点都有相同的值）。

* 因此，我们可以对树进行一次深度优先搜索。当搜索到节点 x 时，我们检查 x 与 x 的每一个子节点之间的边是否满足要求。
* 例如对于左子节点而言，如果其存在并且值与 x 相同，那么我们继续向下搜索该左子节点；如果值与 x 不同，那么我们直接返回 False。


代码实现如下： 
```
var isUnivalTree = function(root) {
    if (!root) return false
    if(root.left){
        if(root.val !== root.left.val || !isUnivalTree(root.left)) return false
    }
    if(root.right){
        if(root.val !== root.right.val || !isUnivalTree(root.right)) return false
    }
    return true
};
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

