---
title: 数据结构：链表
date: 2022-04-11 14:47:07
tags: 数据结构
---
<meta name="referrer" content="no-referrer"/>
## 什么是链表？

* 链表[Linked List]: 链表是由一组不必相连【不必相连： 可以连续也可以不连续】的内存结构【节点】，按照特定顺序连接在一起的抽象的数据类型。

>补充：
抽象数据类型（Abstract Data Type [ADT]）：表示数学中抽象出来的一些操作的集合。
内存结构：内存中的结构，如：struct、特殊内存块...等等之类；

## 链表共分几类？
链表共有三类：单链表、双向链表、循环链表。


链表和核心操作集有3种：插入、删除、查找[遍历]

**`单链表`**：由各个内存结构通过一个Next指针链接在一起组成，每一个内存结构都存在后继内存结构【链尾除外】，内存结构由数据域和Next指针域组成。

单链表实现图示：

文字解析：
* Data数据+Next指针，组成一个单链表的内存结构；
* 第一个内存结构称为链头，最后一个内存结构称为链尾；
* 链尾的Next指针设置为NUll[指向空]；
* 单链表的遍历方向单一【只能从链头一直遍历到链尾】





ListNodelist=newListNode()初始化一个空节点，无值,不提倡此种写法；

L i s t N o d e l i s t = n e w L i s t N o d e ( 0 ) ListNode list=new ListNode(0)ListNodelist=newListNode(0) 初始化一个节点值为0的空节点，最常用最正规写法；

L i s t N o d e l i s t = n u l l ListNode list=nullListNodelist=null 为空，什么都没有，一般不这么写。


## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [java中new ListNode(0)常见用法详细区别（全）](https://blog.csdn.net/weixin_47872288/article/details/118080241)
* [数据结构：链表](https://www.jianshu.com/p/73d56c3d228c)

