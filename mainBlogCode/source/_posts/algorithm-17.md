---
title: algorithm-17： 电话号码的字母组合 (解法)-中等
date: 2023-08-02 13:45:26
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。。

![200px-telephone-keypad2svg.png](https://upload-images.jianshu.io/upload_images/11846892-f152c0ed4ca42b37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**示例1:**


```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例2:**

```
输入：digits = ""
输出：[]
```
**示例3:**
```
输入：digits = "2"
输出：["a","b","c"]
```

## 题解方法一：回溯

**`解题思路`**

* 定义函数 backtrack(combination, nextdigit)，当 nextdigit 非空时，对于 nextdigit[0] 中的每一个字母 letter，执行回溯 backtrack(combination + letter,nextdigit.sclice(1))，直至 nextdigit 为空。最后将 combination 加入到结果中。

![截屏2023-08-02 下午2.50.39.png](https://upload-images.jianshu.io/upload_images/11846892-70b98cc9c29015d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2023-08-02 下午2.52.20.png](https://upload-images.jianshu.io/upload_images/11846892-a8c2a700156feb16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2023-08-02 下午2.53.38.png](https://upload-images.jianshu.io/upload_images/11846892-f3904b2d6afda00b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**代码实现如下：** 
```
var letterCombinations = function(digits) {
    if (!digits) return [];

  const phone = {
    "2": ["a", "b", "c"],
    "3": ["d", "e", "f"],
    "4": ["g", "h", "i"],
    "5": ["j", "k", "l"],
    "6": ["m", "n", "o"],
    "7": ["p", "q", "r", "s"],
    "8": ["t", "u", "v"],
    "9": ["w", "x", "y", "z"],
  };
  let res = []
  var backtrack = (combination, nextDigit) => {
    if(nextDigit.length === 0) {
        res.push(combination)
    } else {
        phone[nextDigit[0]].forEach((letter) => {
            backtrack(combination + letter, nextDigit.slice(1));
        })
    }
  }
  backtrack("", digits);
  return res;
}

```
## 题解方法二：队列

**`解题思路`**
* 先将输入的 digits 中第一个数字对应的每一个字母入队，然后将出队的元素与第二个数字对应的每一个字母组合后入队...直到遍历到 digits 的结尾。最后队列中的元素就是所求结果。

**代码实现如下：**
```
var letterCombinations = function(digits) {
  if (!digits) return [];
  const phone = ['abc', 'def', 'ghi', 'jkl', 'mno', 'pqrs', 'tuv', 'wxyz'];
  let queue = [''];
  let n = digits.length
  for(let i = 0; i < n; i++){
    const digit = digits[i];
    const letters = phone[Number(digit) - 2];
    const queueLength = queue.length;
    for (let j = 0; j < queueLength; j++) { // 数组里的每一项便利 + letters 中的每一项
      const tmp = queue.shift();
      for (let k = 0; k < letters.length; k++) {
        queue.push(tmp + letters[k]);
      }
    }
  }
  return queue
}
```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
* [leeCode-17： 电话号码的字母组合-中等](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)
**`JS知识扩展知识扩展String.prototype.slice():`** 提取某个字符串的一部分，并返回一个新的字符串，且不会改动原字符串。 返回开始索引（包含）到结束索引（不包含）之间的一个子集。
