---
title: DFS_Search
date: 2017-04-13 22:43:28
categories: 编程
tags: [java,算法导论,搜索算法]
---

## 深度优先搜索

深度优先搜索所使用的策略是：只要可能尽可能在图中尽量“深入”。搜索从某个节点v开始探索，然后访问和这个节点邻接的一个未访问过的节点，直到该结点的所有出发边都被发现为止。 若结点v的所有出发边都被发现，搜索则回溯到v的前驱节点，来搜索该前驱节点的出发边。

使用颜色来标记结点的状态。每个节点的初始颜色都是白色，在节点被发现后变成灰色，在其邻接链表被扫描完成后，其颜色变为黑色。该方法保证每一个节点尽在一棵深度优先树中被访问，因此，所有的深度优先树是不相交的。

除了创建一个深度优先搜索森林外，深度优先搜索算法还在每个节点盖上一个时间戳。每个节点v有两个时间记录。一个是v.d记录节点第一次被发现的时间（涂上灰色的时候），第二个时间v.f是对v节点的邻接链表搜索完成的时间（涂上黑色的时候）。这些时间记录提供了重要的图结构信息，能够帮助推断深度优先搜索算法的行为。