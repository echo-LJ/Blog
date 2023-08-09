---
title: leeCode232-用栈实现队列-简单
date: 2023-08-09 14:44:04
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（push、pop、peek、empty）：
实现 MyQueue 类：
* void push(int x) 将元素 x 推到队列的末尾
* int pop() 从队列的开头移除并返回元素
* int peek() 返回队列开头的元素
* boolean empty() 如果队列为空，返回 true ；否则，返回 false

**`peek`**: 查看队列的首个元素，不会移除首个元素，如果队列是空就返回null.
**示例1:**


```
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false

```




## 题解方法一：将值复制到数组中后用双指针法

**`解题思路`**
* 栈实现队列的出队操作效率低下：栈底元素（对应队首元素）无法直接删除，需要将上方所有元素出栈。

* 两个栈可实现将列表倒序：设有含三个元素的栈 A = [1,2,3] 和空栈 B = [] 。若循环执行 A 元素出栈并添加入栈 B ，直到栈 A 为空，则 A = [] , B = [3,2,1] ，即栈 B 元素为栈 A 元素倒序。

* 利用栈 B 删除队首元素：倒序后，B 执行出栈则相当于删除了 A 的栈底元素，即对应队首元素。

* 因此，可以设计栈 A 用于加入队尾操作，栈 B 用于将元素倒序，从而实现删除队首元素。

**`函数设计`**

1、加入队尾 push() ： 将数字 val 加入栈 A 即可。
2、获取队首元素 peek() ：
    * 当栈 B 不为空： B中仍有已完成倒序的元素，因此直接返回 B 的栈顶元素。
    * 否则，当 A 为空： 即两个栈都为空，无元素，因此返回 -1 。
    * 否则： 将栈 A 元素全部转移至栈 B 中，实现元素倒序，并返回栈 B 的栈顶元素。
3、弹出队首元素 pop() ：
    * 执行 peek() ，获取队首元素。
    * 弹出 B 的栈顶元素。
4、队列判空 empty() ： 当栈 A 和 B 都为空时，队列为空。

**代码实现如下：** 
```
var MyQueue = function() {
    this.A = [];
    this.B = [];
};

/** 
 * @param {number} x
 * @return {void}
 */
MyQueue.prototype.push = function(x) {
   this.A.push(x);
};

/**
 * @return {number}
 */
MyQueue.prototype.pop = function() {
    
    const peek = this.peek();
    this.B.pop();
    return peek;
};

/**
 * @return {number}
 */
MyQueue.prototype.peek = function() {
    if (this.B.length) return this.B[this.B.length - 1];
    if (!this.A.length) return -1;
    // 将栈 A 的元素依次移动至栈 B
    while (this.A.length) {
      this.B.push(this.A.pop());
    }
    return this.B[this.B.length - 1];
};

/**
 * @return {boolean}
 */
MyQueue.prototype.empty = function() {
    return !this.A.length && !this.B.length;
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * var obj = new MyQueue()
 * obj.push(x)
 * var param_2 = obj.pop()
 * var param_3 = obj.peek()
 * var param_4 = obj.empty()
 */
```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [leeCode232-用栈实现队列-简单](https://leetcode.cn/problems/implement-queue-using-stacks/description/)

* [Krahets](https://leetcode.cn/problems/implement-queue-using-stacks/solutions/2363977/232-yong-zhan-shi-xian-dui-lie-qing-xi-t-pi4l/)