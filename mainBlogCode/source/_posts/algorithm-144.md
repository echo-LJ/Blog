---
title: leeCode-144： 二叉树的前序遍历： (迭代、递归、Morris遍历三种解法)-简单
date: 2022-04-24 16:25:41
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

## 题目描述： 给你二叉树的根节点 root ，返回它节点值的 前序 遍历。

**示例1:**

![屏幕快照 2022-04-24 下午4.27.55.png](https://upload-images.jianshu.io/upload_images/11846892-ecf6dc20ad7a9e22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,2,3]
输出：[1,2,3]
```

**示例2:**

```
输入：root = []
输出：[]
```

**示例3:**

```
输入：root = [1]
输出：[1]
```
**示例4:**
![屏幕快照 2022-04-24 下午4.29.16.png](https://upload-images.jianshu.io/upload_images/11846892-621ab535519d00b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2]
输出：[1,2]
```

**示例5:**

![屏幕快照 2022-04-24 下午4.29.39.png](https://upload-images.jianshu.io/upload_images/11846892-317cb88b31fa5df5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,2]
输出：[1,2]
```


## 题解方法一：递归法

解题思路：二叉树的前序遍历：按照访问根节点——左子树——右子树的方式遍历这棵树，而在访问左子树或者右子树的时候，我们按照同样的方式遍历，直到遍历完整棵树。因此整个遍历过程天然具有递归的性质，我们可以直接用递归函数来模拟这一过程。

代码实现如下：
```
var preorderTraversal = (root) => {
  let arr = []
  preorder(root, arr);
  return arr
}
var preorder = (root, arr) => {
  if (root === null) return
  arr.push(root.val);
  preorder(root.left, arr)
  preorder(root.right, arr)
}
```

## 题解方法二：迭代

解题思路：我们也可以用迭代的方式实现方法一的递归函数，两种方式是等价的，区别在于递归的时候隐式地维护了一个栈，而我们在迭代的时候需要显式地将这个栈模拟出来，其余的实现与细节都相同。

代码实现如下：
```
var preorderTraversal = function(root) {
    if (!root) return []
    const stack = [], res = []
    stack.push(root)
    while (stack.length) {
        const curr = stack.pop()
        res.push(curr.val)
        if (curr.right) stack.push(curr.right)
        if (curr.left) stack.push(curr.left)
    }
    return res
};
```
下面进行图解分析：

```
stack.push(root)
```
![屏幕快照 2022-04-24 下午5.20.25.png](https://upload-images.jianshu.io/upload_images/11846892-0753974f29e5145b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

stack.length不为0 ，进入while循环
执行第一次循环之后：

![屏幕快照 2022-04-24 下午5.22.23.png](https://upload-images.jianshu.io/upload_images/11846892-83c93dca0a1ef3a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行第二次循环之后：
![屏幕快照 2022-04-24 下午5.24.18.png](https://upload-images.jianshu.io/upload_images/11846892-56773f3caac0d013.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行第3次循环之后：
![屏幕快照 2022-04-24 下午5.25.05.png](https://upload-images.jianshu.io/upload_images/11846892-bbfd616c532c7b42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行第4次循环之后：

![屏幕快照 2022-04-24 下午5.25.46.png](https://upload-images.jianshu.io/upload_images/11846892-aec07ae65b31f893.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行第5次循环之后：
![屏幕快照 2022-04-24 下午5.26.54.png](https://upload-images.jianshu.io/upload_images/11846892-3f4036c490b19e34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行第6次循环之后：

![屏幕快照 2022-04-24 下午5.27.45.png](https://upload-images.jianshu.io/upload_images/11846892-5cfe4c37d9b9dcdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


结束循环，执行完毕！！


## 题解方法三：Morris遍历 !!没看懂

解题思路：Morris遍历的详细解释+注释版

一些前置知识：

前驱节点，如果按照中序遍历访问树，访问的结果为ABC，则称A为B的前驱节点，B为C的前驱节点。
前驱节点pre是curr左子树的最右子树（按照中序遍历走一遍就知道了）。
由此可知，前驱节点的右子节点一定为空。
主要思想：

树的链接是单向的，从根节点出发，只有通往子节点的单向路程。

中序遍历迭代法的难点就在于，需要先访问当前节点的左子树，才能访问当前节点。

但是只有通往左子树的单向路程，而没有回程路，因此无法进行下去，除非用额外的数据结构记录下回程的路。

在这里可以利用当前节点的前驱节点，建立回程的路，也不需要消耗额外的空间。

根据前置知识的分析，当前节点的前驱节点的右子节点是为空的，因此可以用其保存回程的路。

但是要注意，这是建立在破坏了树的结构的基础上的，因此我们最后还有一步“消除链接”’的步骤，将树的结构还原。

重点过程： 当遍历到当前节点curr时，使用cuur的前驱节点pre

标记当前节点是否访问过
记录回溯到curr的路径（访问完pre以后，就应该访问curr了）
以下为我们访问curr节点需要做的事儿：

访问curr的节点时候，先找其前驱节点pre
找到前驱节点pre以后，我们根据其右指针的值，来判断curr的访问状态：
pre的右子节点为空，说明curr第一次访问，其左子树还没有访问，此时我们应该将其指向curr，并访问curr的左子树
pre的右子节点指向curr，那么说明这是第二次访问curr了，也就是说其左子树已经访问完了，此时将curr.val加入结果集中
更加细节的逻辑请参考代码：

```
public List<Integer> method3(TreeNode root) {
        List<Integer> ans=new LinkedList<>();
        while(root!=null){
            //没有左子树，直接访问该节点，再访问右子树
            if(root.left==null){
                ans.add(root.val);
                root=root.right;
            }else{
            //有左子树，找前驱节点，判断是第一次访问还是第二次访问
                TreeNode pre=root.left;
                while(pre.right!=null&&pre.right!=root)
                    pre=pre.right;
                //是第一次访问，访问左子树
                if(pre.right==null){
                    pre.right=root;
                    root=root.left;
                }
                //第二次访问了，那么应当消除链接
                //该节点访问完了，接下来应该访问其右子树
                else{
                    pre.right=null;
                    ans.add(root.val);
                    root=root.right;
                }
            }
        }
        return ans;
    }    

```


参考地址：
* [二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)
* https://leetcode-cn.com/problems/binary-tree-preorder-traversal/solution/bao-ni-hui-de-dui-ge-lei-ti-jie-zhong-ge-mlgr/





