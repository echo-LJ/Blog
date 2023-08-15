---
title: leeCode-946： 验证栈序列： (模拟解法)-中等
date: 2023-08-15 13:40:16
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给定 pushed 和 popped 两个序列，每个序列中的 值都不重复，只有当它们可能是在最初空栈上进行的推入 push 和弹出 pop 操作序列的结果时，返回 true；否则，返回 false 。


**示例1:**


```
输入：pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
输出：true
解释：我们可以按以下顺序执行：
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1

```

**示例2:**

```
输入：pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
输出：false
解释：1 不能在 2 之前弹出。
```



## 题解方法一：模拟

**`解题思路`**
* 初始化： 辅助栈 stack ，弹出序列的索引 i。 
* 遍历压栈序列： 各元素记为 num 。
    a、元素 num 入栈。
    b、循环出栈：若 stack 的栈顶元素 === 弹出序列元素 popped[i] ，则执行出栈与 i++ 。
* 返回值： 若 stack 为空，则此弹出序列合法。

代码实现如下： 
```
function validateStackSequences(pushed, popped) {
  let stack = [];
  let i = 0;
  for (let num of pushed) {
    stack.push(num); // num 入栈
    while (stack.length && stack[stack.length - 1] === popped[i]) { // 循环判断与出栈
      stack.pop();
      i += 1;
    }
  }
  return stack.length === 0;
}
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

## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [leeCode-946： 验证栈序列： (模拟解法)-中等](https://leetcode.cn/problems/validate-stack-sequences/solutions/2362056/946-yan-zheng-zhan-xu-lie-mo-ni-qing-xi-wpxi6/)