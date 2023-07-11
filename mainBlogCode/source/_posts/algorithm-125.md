---
title: leeCode-125：有效的回文-简单
date: 2023-07-11 19:50:43
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个 回文串 。字母和数字都属于字母数字字符。给你一个字符串 s，如果它是 回文串 ，返回 true ；否则，返回 false 。

**示例1:**


```
输入: s = "A man, a plan, a canal: Panama"
输出：true
解释："amanaplanacanalpanama" 是回文串。

```

**示例2:**

```
输入：s = "race a car"
输出：false
解释："raceacar" 不是回文串。
```

**示例3:**

```
输入：s = " "
输出：true
解释：在移除非字母数字字符之后，s 是一个空字符串 "" 。
由于空字符串正着反着读都一样，所以是回文串。
```

## 题解方法一：双指针

**`解题思路`**
* 所以第一步先把大写转化成小写
* 第二步就开始双指针滑动，这里写一个check函数检查这个字符是否合法，不合法就下一个，比较完了都没有return false，就代表这是个回文串。



代码实现如下： 
```var isPalindrome = function(s) {
    let left = 0; let right = s.length -1
    s = s.toLowerCase()
    while(left < right){
      let l = s.charAt(left)
      let r = s.charAt(right)
      if(!isCheck(l)) {
        left++
        continue;
      }
      if(!isCheck(r)) {
        right--
        continue;
      }
      if(l == r) {
        left++
        right--
      } else{
        return false
      }
    }
    return true
};
var isCheck  = function (c) { 
  if((c >= "a") && (c <= "z") || (c >= "0") && (c <= "9")) {
      return true;
    } else{
      return false
    }
  
}

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [力扣-回文数-简单](https://leetcode.cn/problems/palindrome-number/solutions/281686/hui-wen-shu-by-leetcode-solution/)