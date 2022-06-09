---
title: leeCode-104： 二叉树的最大深度：(深度优先搜索)-简单
date: 2022-06-08 16:42:12
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定一个二叉树，找出其最大深度。 二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

* 说明: 叶子节点是指没有子节点的节点。


**示例1:**


给定二叉树[3,9,20,null,null,15,7] 它的最大深度返回 3。



### 题解方法一： 深度优先搜索

**`题目分析：`**
* 如果我们知道了左子树和右子树的最大深度 l 和 r，那么该二叉树的最大深度即为 max(l,r) + 1,代码实现如下：
```
maxDepth = function(root) {
        if (!root) return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }


```

### 题解方法二： 广度优先搜索

**`题目分析：`**
* 如果我们知道了左子树和右子树的最大深度 l 和 r，那么该二叉树的最大深度即为 max(l,r) + 1,代码实现如下：
```
/**
 * @param {TreeNode} root
 * @return {number}
 */
var maxDepth = function (root) {
  if (root === null) {
    return 0;
  }

  const queue = []; // BFS 队列
  queue.push(root); // 第一层 根节点 入队列

  let ans = 0;
  while (queue.length) {
    ans++; // 每遍历一层，深度 +1

    let size = queue.length; // 本层节点的数量

    while (size--) { // 遍历本层所有节点
      const node = queue.shift();  // 本层节点依次出队列
      // 当前节点的左子节点入队列
      if (node.left) {
        queue.push(node.left);
      }
      // 当前节点的由子节点入队列
      if (node.right) {
        queue.push(node.right);
      }
    }
  }

  return ans;
};


```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/symmetric-tree/solution/dui-cheng-er-cha-shu-by-leetcode-solution/

