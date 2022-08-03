---
title: leeCode-951： 翻转等价二叉树： (递归、标准态遍历两种解法)-中等
date: 2022-08-03 13:53:41
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  我们可以为二叉树 T 定义一个 翻转操作 ，如下所示：选择任意节点，然后交换它的左子树和右子树。 只要经过一定次数的翻转操作后，能使 X 等于 Y，我们就称二叉树 X 翻转 等价 于二叉树 Y。 这些树由根节点 root1 和 root2 给出。如果两个二叉树是否是翻转 等价 的函数，则返回 true ，否则返回 false 。


**示例1:**

![tree_ex.png](https://upload-images.jianshu.io/upload_images/11846892-dbec13c5ee054474.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root1 = [1,2,3,4,5,6,null,null,null,7,8], root2 = [1,3,2,null,6,4,5,null,null,null,null,8,7]
输出：true
解释：我们翻转值为 1，3 以及 5 的三个节点。

```

**示例2:**

```
输入: root1 = [], root2 = []
输出: true
```

**示例3:**

```
输入: root1 = [], root2 = [1]
输出: false
```

## 题解方法一：递归

**`解题思路`**
如果二叉树 root1，root2 根节点值相等，那么只需要检查他们的孩子是不是相等就可以了。
* 如果 root1 或者 root2 是 null，那么只有在他们都为 null 的情况下这两个二叉树才等价。
* 如果 root1，root2 的值不相等，那这两个二叉树的一定不等价。
* 如果以上条件都不满足，也就是当 root1 和 root2 的值相等的情况下，需要继续判断 root1 的孩子节点是不是跟 root2 的孩子节点相当。因为可以做翻转操作，所以这里有两种情况需要去判断。

代码实现如下： 
```
var flipEquiv = function(root1, root2) {
    if(root1 === root2) return true
    if(root1 == null || root2 == null || root1.val !== root2.val) return false
    return (flipEquiv(root1.left, root2.left) && flipEquiv(root1.right, root2.right) ) || (flipEquiv(root1.left, root2.right) && flipEquiv(root1.right, root2.left))
};
```


## 题解方法二：标准态遍历

**`解题思路`**
让树中所有节点的左孩子都小于右孩子，如果当前不满足就翻转。我们把这种状态的二叉树称为 标准态。所有等价二叉树在转换成标准态后都是完全一样的。

用深度优先遍历来对比这两棵树在标准态下是否完全一致。对于两颗等价树，在标准态下遍历的结果一定是一样的。


代码实现如下：

```

var flipEquiv = function(root1, root2) {
    let list1 = [] 
    let list2 = []
    dfs(root1, list1);
    dfs(root2, list2);
    return list1.length === list2.length && list1.every((v, i) =>{return v === list2[i]})
}
var dfs = function(node, list){
    if(node !== null) {
        list.push(node.val)
        let l = node.left !== null ? node.left.val : -1
        let r = node.right !== null ? node.right.val : -1
        if(l<r){
            dfs(node.left, list)
            dfs(node.right, list)
        } else {
            dfs(node.right, list)
            dfs(node.left, list)
        }
        list.push(null);
    }
}


```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：[https://leetcode.cn/problems/flip-equivalent-binary-trees/solution/fan-zhuan-deng-jie-er-cha-shu-by-leetcode/](https://leetcode.cn/problems/flip-equivalent-binary-trees/solution/fan-zhuan-deng-jie-er-cha-shu-by-leetcode/)