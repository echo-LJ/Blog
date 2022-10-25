---
title: leeCode-110：平衡二叉树（自顶向下的递归、自底向上的递归）-简单
date: 2022-06-21 13:43:24
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给定一个二叉树，判断它是否是高度平衡的二叉树。

* 本题中，一棵高度平衡二叉树定义为： 一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 。


**示例1:**

![截屏2022-06-21 下午1.45.43.png](https://upload-images.jianshu.io/upload_images/11846892-c613c69b2d12b1b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [3,9,20,null,null,15,7]
输出：true
```
**示例2:**
![截屏2022-06-21 下午1.46.06.png](https://upload-images.jianshu.io/upload_images/11846892-72e76d094d937b31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2,2,3,3,null,null,4,4]
输出：false

```
**示例3:**
```
输入：root = []
输出：true

```

## 题解方法一：自顶向下的递归

代码实现如下： 
```
var isBalanced = function(root) {
    if(!root) return true
    return Math.abs(helper(root.left) - helper(root.right)) <=1 && isBalanced(root.left) && isBalanced(root.right) 
};

var helper = (root) =>{
    if(!root) return 0
    return Math.max(helper(root.left), helper(root.right)) + 1
}
```

## 题解方法二：自底向上的递归

代码实现如下： 
```
var isBalanced = function(root) {
    return helper(root) >= 0
};

var helper = (root) => {
    if(!root) return 0
    let left = helper(root.left)
    let right = helper(root.right)
    if(left == -1 || right == -1 || Math.abs(left-right) >1) {
        return -1
    } else {
        return Math.max(left, right) + 1;
    }
}

```

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
https://leetcode.cn/problems/balanced-binary-tree/solution/ping-heng-er-cha-shu-by-leetcode-solution/











