---
title: leeCode-101：对称二叉树：（递归-深度优先遍历、迭代-广度优先遍历）-初级
date: 2022-06-03 15:47:48
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给你一个二叉树的根节点 root ， 检查它是否轴对称。。


**示例1:**

![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-d591662ee5366713.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

**示例2:**


![demo2.png](https://upload-images.jianshu.io/upload_images/11846892-1f7177dc91ab093e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```



输入：root = [1,2,2,null,3,null,3]
输出：false

```

**示例3:**
```

输入：root = []
输出：false

```
### 题解方法一： 递归-深度遍历

`两个树在什么情况下互为镜像？`
* 它们的两个根结点具有相同的值
* 每个树的右子树都与另一个树的左子树镜像对称

![exp1.png](https://upload-images.jianshu.io/upload_images/11846892-472f8698736244f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 通过「同步移动」两个指针的方法来遍历这棵树，p 指针和 q 指针一开始都指向这棵树的根，随后 p 右移时，q 左移，p 左移时，q 右移。每次检查当前 p 和 q 节点的值是否相等，如果相等再判断左右子树是否对称。

代码实现如下：
```
var isSymmetric = function(root) {
    if (root==null)
        return true;
    else {
        return check(root, root)
    }
    
};
var check = function(p, q) {
    if(!p && !q) return true
    if(!p || !q) return false
    return p.val == q.val && check(p.left, q.right) &&  check(p.right, q.left)
};
```

## 题解方法二：迭代-广度优先遍历



代码实现如下：
```
const check = (u, v) => {
    const q = [];
    q.push(u),q.push(v);

    while (q.length) {
        u = q.shift();
        v = q.shift();

        if (!u && !v) continue;
        if ((!u || !v) || (u.val !== v.val)) return false;

        q.push(u.left); 
        q.push(v.right);

        q.push(u.right); 
        q.push(v.left);
    }
    return true;
}
var isSymmetric = function(root) {
    return check(root, root);
};

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [对称二叉树](https://leetcode.cn/problems/symmetric-tree/solution/dui-cheng-er-cha-shu-by-leetcode-solution/)
