---
title: 算法
date: 2021-01-26 18:25:37
tags:
---
<meta name="referrer" content="no-referrer"/>

## 1. 使用队列实现击鼓传花
**击鼓传花规则：**几个朋友一次玩游戏，从1开始依次数数，数到敏感数字的人被淘汰，淘汰的人后面的一个人从1开始，最后剩下的人则胜利

**实现思路：**
* 队列的特征（队尾插入，对头删除）push shift

```
function passName(nameList, num){
  while(nameList.length > 1) {
    for (let i = 0; i< num.length; i++) {
      nameList.push(nameList.shift())
    }
    nameList.pop()
  }
  return nameList[0];
}
```
*
