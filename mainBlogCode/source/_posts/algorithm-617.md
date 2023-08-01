---
title: leeCode-617：合并二叉树：（深度优先搜索DFS、广度优先搜索BFS）-初级
date: 2022-06-01 13:31:04
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给你两棵二叉树：root1 和root2。想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，不为null的节点将直接作为新二叉树的节点。返回合并后的二叉树。

** 注意: 合并过程必须从两个树的根节点开始。**

**示例1:**

![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-8efae1b60e603a36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
输出：[3,4,5,5,4,null,7]
```

**示例2:**
```

输入：root1 = [1], root2 = [1,2]
输出：[2,2]

```
### 题解方法一： 深度优先搜索DFS

`从根节点同时遍历两个二叉树，将对应的的节点进行合并`

* 两个节点都为空: 新节点也为空
* 只有一个节点为空: 新节点等于不为空的节点
* 两个节点都不为空：新节点为两节点之和。

代码实现如下：
```
var mergeTrees = function(root1, root2) {
    if (!root1) return root2
    if (!root2) return root1
    let merged = new TreeNode(root1.val + root2.val);
    merged.left = mergeTrees(root1.left, root2.left);
    merged.right = mergeTrees(root1.right, root2.right);
    return merged
};
```

## 题解方法二：广度优先搜索BFS

**`首先判断两个二叉树是否为空`**

* 如果两个二叉树都为空: 合并后的二叉树也为空
* 如果只有一个二叉树为空: 则合并后的二叉树为另一个非空的二叉树
* 如果两个二叉树都不为空: 首先计算合并后的跟节点的值，从合并后的二叉树与两个原始的二叉树进行BFS，从根节点同时遍历每个二叉树，并将对应的节点进行合并。

**`使用三个队列分别存储合并后的节点、原始两个二叉树的节点`**
* 初始时：将每个二叉树的根节点分别加入相应的队列。
* 每次从每个队列中取出一个节点，判断两个原始二叉树的节点的左右子节点是否为空，
* 如果原始的两个二叉树左子节点都不为空：则合并后的二叉树为两个节点值之和，在创建合并后的二叉树的左子节点之后，将每个二叉树中的左子节点都加入相应的队列。（右子树同理）
* 如果原始的两个二叉树左子节点只有一个节点为空：则合并后的二叉树的左子树即为另一个原始二叉树的左子树，此时也不需要对非空左子树继续遍历，因此不需要将左子节点加入队列。（右子树同理）


代码实现如下：
```
var mergeTrees = function(root1, root2) {
    if (!root1) return root2
    if (!root2) return root1
    let merged = new TreeNode(root1.val + root2.val);
    let stack = [merged], stack1 = [root1], stack2 = [root2]
    while(stack1.length > 0) {
        let node = stack.shift()
        let node1 = stack1.shift()
        let node2 = stack2.shift()
        let left1 = node1.left
        let left2 = node2.left
        let right1 = node1.right
        let right2 = node2.right
        if (left1 !== null|| left2!== null) {
            if (!left1) {
                node.left = left2
            } else if (!left2) {
                node.left = left1
            } else {
                node.left = new TreeNode(left1.val + left2.val);
                stack.push(node.left)
                stack1.push(left1)
                stack2.push(left2)
            }
        }
        if (right1!== null || right2!== null) {
            if (!right1) {
                node.right = right2
            } else if (!right2) {
                node.right = right1
            } else {
                node.right = new TreeNode(right1.val + right2.val);
                stack.push(node.right)
                stack1.push(right1)
                stack2.push(right2)
            }
        }
    }
    return merged

};

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
https://leetcode.cn/problems/merge-two-binary-trees/solution/he-bing-er-cha-shu-by-leetcode-solution/

* [合并二叉树：（深度优先搜索DFS、广度优先搜索BFS）](https://leetcode.cn/problems/merge-two-binary-trees/)