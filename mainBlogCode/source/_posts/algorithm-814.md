---
title: leeCode-814： 二叉树剪枝（递归）-中等
date: 2022-06-22 10:14:24
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给你二叉树的根结点 root ，此外树的每个结点的值要么是 0 ，要么是 1 。返回移除了所有不包含 1 的子树的原二叉树。节点 node 的子树为 node 本身加上所有 node 的后代。



**示例1:**

![截屏2022-06-22 上午10.16.08.png](https://upload-images.jianshu.io/upload_images/11846892-4e8cbb4ed5602e55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,0,0,1]
输出：[1,null,0,null,1]
解释：
只有红色节点满足条件“所有不包含 1 的子树”。 右图为返回的答案。

```
**示例2:**
![截屏2022-06-22 上午10.16.24.png](https://upload-images.jianshu.io/upload_images/11846892-e488e8c1231cab4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,0,1,0,0,0,1]
输出：[1,null,1,null,1]

```
**示例3:**
![截屏2022-06-22 上午10.16.50.png](https://upload-images.jianshu.io/upload_images/11846892-1324a3ebd53e82db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,1,0,1,1,0,1,0]
输出：[1,1,0,1,1,null,1]

```

## 题解方法一：递归

`解题思路:` 我们用递归来解决这个问题， 用containsOne(node) 函数来判断以 node 为根的子树中是否包含 1

* node节点本身不包含1 ，并且node节点的左右子树不包含1

* 当node本身不包含1， 但是node的右孩子包含1 ，左孩子不包含1， 则将左孩子设置为null

代码实现如下： 
```


var pruneTree = function(root) {
    return containsOne(root) ? root : null
};
var containsOne = function(root){
    if(!root) return false
    let left = containsOne(root.left)
    let right = containsOne(root.right)
    if(!left) root.left = null
    if(!right) root.right = null
    return root.val ==1 || left || right
}

```

## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [二叉树剪枝](https://leetcode.cn/problems/binary-tree-pruning/solution/er-cha-shu-jian-zhi-by-leetcode/)












