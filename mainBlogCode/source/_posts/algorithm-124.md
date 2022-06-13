---
title: leeCode-124： 二叉树中的最大路径和：(广度优先搜索)-困难
date: 2022-06-13 13:57:09
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点，路径和 是路径中各节点值的总和，给你一个二叉树的根节点 root ，返回其 最大路径和 。


**示例1:**


![截屏2022-06-13 下午2.00.05.png](https://upload-images.jianshu.io/upload_images/11846892-2f75b887ea164dd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6
```
**示例2:**
![截屏2022-06-13 下午2.01.18.png](https://upload-images.jianshu.io/upload_images/11846892-04abb51ec6cb6f4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42
```

**示例3:**
```
输入：root = []
输出：0
```

### 题解方法一： 广度优先搜索

**`解题思路：`**
* 广度优先遍历是按层层推进的方式，遍历每一层的节点。题目要求的是返回每一层的节点值，所以这题用广度优先来做非常合适。
* 广度优先需要用队列作为辅助结构，我们先将根节点放到队列中，然后不断遍历队列。
![截屏2022-06-10 下午5.36.13.png](https://upload-images.jianshu.io/upload_images/11846892-0aff3c306e1bf899.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 首先拿出根节点，如果左子树/右子树不为空，就将他们放入队列中。第一遍处理完后，根节点已经从队列中拿走了，而根节点的两个孩子已放入队列中了，现在队列中就有两个节点 2 和 5。

![截屏2022-06-10 下午5.36.56.png](https://upload-images.jianshu.io/upload_images/11846892-73de06778ba9018b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 第二次处理，会将 2 和 5 这两个节点从队列中拿走，然后再将 2 和 5 的子节点放入队列中，现在队列中就有三个节点 3，4，6。

![截屏2022-06-10 下午5.37.23.png](https://upload-images.jianshu.io/upload_images/11846892-5f4843ca5dcab1cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 我们把每层遍历到的节点都放入到一个结果集中，最后返回这个结果集就可以了。
代码实现如下：
```
var levelOrder = function(root) {
    const ret = [];
    if (!root) {
        return ret;
    }

    const q = [];
    q.push(root);
    while (q.length !== 0) {
        const currentLevelSize = q.length;
        ret.push([]);
        for (let i = 1; i <= currentLevelSize; ++i) {
            const node = q.shift();
            ret[ret.length - 1].push(node.val);
            if (node.left) q.push(node.left);
            if (node.right) q.push(node.right);
        }
    }
        
    return ret;
};

```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/solution/236-er-cha-shu-de-zui-jin-gong-gong-zu-xian-hou-xu/
BFS 的使用场景总结：层序遍历、最短路径问题： https://leetcode.cn/problems/binary-tree-level-order-traversal/solution/bfs-de-shi-yong-chang-jing-zong-jie-ceng-xu-bian-l/



