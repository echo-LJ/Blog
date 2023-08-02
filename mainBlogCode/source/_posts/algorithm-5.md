---
title: leeCode-5：最长回文子串-中等
date: 2023-08-01 10:04:48
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你一个字符串 s，找到 s 中最长的回文子串。如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。

* 例如，121 是回文，而 123 不是。

**示例1:**


```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

**示例2:**

```
输入：s = "cbbd"
输出："bb"
```


## 题解方法一：暴力解法

**`解题思路`**
* 特判：如果长度大于 2， 直接返回参数值。
* 存储最长回文串ans、最大回文串长度max，进行遍历，如果截取的长度> 存储的长度 && 截取的是回文串 则更新ans和max。


**代码实现如下：**
```
var longestPalindrome = function(s) {
    let ans = 0 // 最大回文数
    let max = 0 //最大回文数长度
    let len = s.length
    if(len < 2) return s
    for(let i = 0; i<len; i++){
        for(let j = 1; j<=len; j++){
            let test = s.substring(i, j);
            let testLen = test.length
            if(testLen > max && isPalindromic(test)) {
                ans = test
                max = testLen
            }
        }
    }
    return ans
};
var isPalindromic = function(s){
    let len = s.length
    for(let i = 0; i<len/2; i++){
        if (s.charAt(i) != s.charAt(len - i - 1)) {
				return false;
		}
    }
    return true
}

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [最长回文子串-中等](https://leetcode.cn/problems/longest-palindromic-substring/description/)

**`JS知识扩展String.substring():`** 返回开始索引（包含）到结束索引（不包含）之间的一个子集。
**`JS知识扩展String.charAt():`** 返回给定索引的字符串。
