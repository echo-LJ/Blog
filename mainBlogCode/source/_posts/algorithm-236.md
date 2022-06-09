---
title: leeCode-236： 二叉树的最近公共祖先：(深度优先搜索)-中等
date: 2022-06-09 14:30:11
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。


**示例1:**


![截屏2022-06-09 下午2.32.25.png](https://upload-images.jianshu.io/upload_images/11846892-a022a708d992b331.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出：3
解释：节点 5 和节点 1 的最近公共祖先是节点 3 。
```
**示例2:**

![截屏2022-06-09 下午2.33.18.png](https://upload-images.jianshu.io/upload_images/11846892-13d621ec03b9c0ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出：5
解释：节点 5 和节点 4 的最近公共祖先是节点 5 。因为根据定义最近公共祖先节点可以为节点本身。
```

**示例3:**
```
输入：root = [1,2], p = 1, q = 2
输出：1
```

### 题解方法一： 深度优先搜索

**`解题思路：`**
* 祖先的定义： 若节点 p 在节点 root 的左（右）子树中，或 p = root ，则称 root 是 p 的祖先。
* 最近公共祖先的定义： 设节点 root 为节点 p, q 的某公共祖先，若其左子节点 root.left 和右子节点 root.right 都不是 p,q 的公共祖先，则称 root 是 “最近的公共祖先” 。

![截屏2022-06-09 下午4.54.00.png](https://upload-images.jianshu.io/upload_images/11846892-b0a07182c9fbbe3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据以上定义，若 root 是 p, q 的 最近公共祖先 ，则只可能为以下情况之一：

p 和 q 在 root 的子树中，且分列 root 的 异侧（即分别在左、右子树中）；
p = root ，且 q 在 root 的左或右子树中；
q = root ，且 p 在 root 的左或右子树中；


![截屏2022-06-09 下午4.55.44.png](https://upload-images.jianshu.io/upload_images/11846892-d09d6f30e34ca0d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

考虑通过递归对二叉树进行先序遍历，当遇到节点 p 或 q 时返回。从底至顶回溯，当节点 p, q 在节点 root 的异侧时，节点 root 即为最近公共祖先，则向上返回 root。

递归解析：
1、终止条件：
    1> 当越过叶节点，则直接返回 null ；
    2> 当 root 等于 p, q 则直接返回 root ；
2、递推工作：
    1> 开启递归左子节点，返回值记为 left ；
    2> 开启递归右子节点，返回值记为 right ；
3、返回值： 根据left 和right,可展开为四种情况；
    1> 同时为空，说明左、右子树都不包含p、q,则直接返回 null ；
    2> 同时不为空：说明p、q分别在两侧，因此root为最近公共祖先，返回root;
    3> left为空，right不为空：p、q都不在左子树中，直接返回right，具体分析为一下两种情况
        1）p、q其中一个在root的右子树中，其中right指向p或者q
        2) p,q 两节点都在 root 的 右子树 中，此时的 right 指向 最近公共祖先节点 ；
    4> left不为空，right为空，同上。


代码实现如下：
```
const lowestCommonAncestor = (root, p, q) => {
    if(root == null || root == p || root == q) return root;
        let left = lowestCommonAncestor(root.left, p, q);
        let right = lowestCommonAncestor(root.right, p, q);
        if(left == null && right == null) return null; // 1.
        if(left == null) return right; // 3.
        if(right == null) return left; // 4.
        return root; // 2. if(left != null and right != null)
    }

```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/solution/236-er-cha-shu-de-zui-jin-gong-gong-zu-xian-hou-xu/


