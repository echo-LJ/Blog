---
title: algorithm-20： 有效的括号 (辅助栈法)-简单
date: 2023-08-02 15:27:16
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

* 左括号必须用相同类型的右括号闭合。
* 左括号必须以正确的顺序闭合。
* 每个右括号都有一个对应的相同类型的左括号。


**示例1:**


```
输入：s = "()"
输出：true
```

**示例2:**

```
输入：s = "()[]{}"
输出：true
```
**示例3:**

```
输入：s = "(]"
输出：false
```

## 题解方法一：

**`解题思路`**
* 利用`栈先入后出特点` 与`本题括号排序特点一致`,，即若遇到左括号入栈，遇到右括号时将对应栈顶左括号出栈，则遍历完所有括号后 stack 仍然为空；
* 建立哈希表 dic 构建左右括号对应关系：keykeykey 左括号，valuevaluevalue 右括号；这样查询 222 个括号是否对应只需 O(1) 时间复杂度；建立栈 stack，遍历字符串 s 并按照算法流程一一判断。

**`算法流程`**

如果 c 是左括号，则入栈 push；
否则通过哈希表判断括号对应关系，若 stack 栈顶出栈括号 stack.pop() 与当前遍历括号 c 不对应，则提前返回 false。

**代码实现如下：** 
```
var isValid = function(s) {
    const dic = {
            '{': '}',
            '[': ']',
            '(': ')',
            '?': '?'
        };
        const stack = ['?'];
        for (let c of s) {
            if (c in dic) {
                stack.push(c);
            } else if (dic[stack.pop()] != c) {
                return false;
            }
        }
        return stack.length === 1;
};
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [有效的括号-辅助栈法](https://leetcode.cn/problems/4sum/description/)

