
---
title: leeCode-14：最长公共前缀-简单
date: 2023-07-06 20:33:38
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  编写一个函数来查找字符串数组中的最长公共前缀。如果不存在公共前缀，返回空字符串 ""。

**示例1:**


```
输入：strs = ["flower","flow","flight"]
输出："fl"
```

**示例2:**

```
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```


## 题解方法一：横向扫描

**`解题思路`**
* 用 LCP(S1…Sn)表示S1…Sn的最长公共前缀
* 可以得到以下结论：LCP(S1…Sn) = LCP(LCP(LCP(S1,S2)S3),...Sn)
* 基于该结论，可以得到一种查找字符串数组中的最长公共前缀的简单方法:
* 依次遍历字符串数组中的每个字符串，对于每个遍历到的字符串，更新最长公共前缀，当遍历完所有的字符串以后，即可得到字符串数组中的最长公共前缀。
![14_fig1.png](https://upload-images.jianshu.io/upload_images/11846892-51c858fd347c8bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 如果在尚未遍历完所有的字符串时，最长公共前缀已经是空串，则最长公共前缀一定是空串，因此不需要继续遍历剩下的字符串，直接返回空串即可。

代码实现如下： 
```
var longestCommonPrefix = function(strs) {
    if(strs.length === 0) return ''
    let pre = strs[0];
    for(let i = 1; i< strs.length; i++) {
        pre = getlongestCommonPrefix(pre, strs[i])
        if(pre.length === 0) return ''
    }
    return pre
};
var getlongestCommonPrefix = function(str1, str2){
    let length = Math.min(str1.length, str2.length)
    let index = 0
    while(index<length && str1[index] === str2[index] ){
        index +=1
    }
    return str1.slice(0,index)
}
```
## 题解方法二：纵向扫描

**`解题思路`**
纵向扫描时，从前往后遍历所有字符串的每一列，比较相同列上的字符是否相同，如果相同则继续对下一列进行比较，如果不相同则当前列不再属于公共前缀，当前列之前的部分为最长公共前缀。
![14_fig2.png](https://upload-images.jianshu.io/upload_images/11846892-63a2afb473e3cd05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现如下： 
```
var longestCommonPrefix = function(strs) {
    if(strs.length === 0 || strs === null) return ''
    let length = strs[0].length;
    let count = strs.length
    for(let i = 0; i< length; i++) {
        let curr = strs[0].charAt(i)
        for(let j =1; j<count; j++) {
            if(i === strs[j].length || strs[j].charAt(i) !== curr) {
                return strs[0].substring(0, i)
            }
        }
        
    }
    return strs[0]
};
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [力扣-最长公共前缀-简单](https://leetcode.cn/problems/longest-common-prefix/)