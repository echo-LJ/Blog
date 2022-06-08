---
title: leeCode-98： 二叉搜索树：(递归、中序遍历两种解法)-初级
date: 2022-06-04 17:32:26
tags: 算法
---


tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

`有效 二叉搜索树定义如下：`

* 节点的左子树只包含 小于 当前节点的数。
* 节点的右子树只包含 大于 当前节点的数。
* 所有左子树和右子树自身必须也是二叉搜索树。

**示例1:**

![demo.png](https://upload-images.jianshu.io/upload_images/11846892-c31fe8189404a12a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [2,1,3]
输出：true
```

**示例2:**

```
输入：root = [5,1,4,null,null,3,6]
输出：false
解释：根节点的值是 5 ，但是右子节点的值是 4 。

```

### 题解方法一： 递归-深度遍历

`如果该二叉树的左子树不为空，则左子树上所有节点的值均小于它的根节点的值； 若它的右子树不空，则右子树上所有节点的值均大于它的根节点的值；它的左右子树也为二叉搜索树。`


代码实现如下：
```
var helper = function(root, lower, upper) {
    if (root==null)
        return true;
    if ((root.val <= lower)||(root.val >= upper)) return false
    return helper(root.left, lower, root.val) && helper(root.right, root.val, upper)
    
};
var isValidBST = function(root) {
    return helper(root, -Infinity, Infinity);
};

```

## 题解方法二:中序遍历

`根据题意我们可以知道：左节点 < 根节点 < 右节点 我们可以采用中序遍历的方法，依次遍历左节点，中节点、右节点。`




代码实现如下：
```
var isValidBST = function(root) {
    let stack = [];
    let inorder = -Infinity;

    while (stack.length || root !== null) {
        while (root !== null) {
            stack.push(root);
            root = root.left;
        }
        root = stack.pop();
        // 如果中序遍历得到的节点的值小于等于前一个 inorder，说明不是二叉搜索树
        if (root.val <= inorder) {
            return false;
        }
        inorder = root.val;
        root = root.right;
    }
    return true;
};

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/symmetric-tree/solution/dui-cheng-er-cha-shu-by-leetcode-solution/
