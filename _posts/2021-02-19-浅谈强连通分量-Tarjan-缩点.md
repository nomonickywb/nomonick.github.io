---
layout:     post                    # 使用的布局（不需要改）
title:      浅谈强连通分量 Tarjan 缩点                # 标题 
subtitle:   Basic course for Strongly Connected Components and Tarjan Indentations. #副标题
date:       2021-02-19              # 时间
author:     nomonick                        # 作者
header-img: img/night.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    -  tarjan
    -  course
    -  Strongly Connected Components
---


啊这，这些 Tarjan 的题目怎么都交不了题解 ？？ 

Tarjan 也太火爆了吧。

---

### What's Tarjan ?

首先，

这张图是很适合 Tarjan 初学者观赏的一张图。

![](https://cdn.luogu.com.cn/upload/image_hosting/re8x5cu1.png)

如果我们在操作的时会看到 { 2，3，4 } 三个点是可以互相到达的,这就是一组强连通分量。

同时只有一个点其也必然是一个强连通分量，（自己一定可以到达自己），那么我们为什么不把它们缩成一个点呢 ？？ 这样一张图就不再拥有环，即至少有一个点的入度为零，会形成一片森林。

这样就可以跑 toposort ， dfs搜索树 ……………… 了，难道不香吗？

跑完 Tarjan 了还要跑图，留着 DAG 回家过年吗？？ （by 我机房一位 dalao）

---

### When will we use Tarjan ?

所以， Tarjan 什么时候用呢？？

在有向图中（必须是有向图再能用 Tarjan 缩点，当然有的算法用 Tarjan 是无向图，比如 Tarjan 割点）

---

### Why we use Tarjan ?

因为她很快！

那么 Tarjan 的时间复杂度多大 ： O（n）

没错，只有 O(n) ，因为每个点之遍历一遍，只有一个时间戳。

---

### How to use it ?

最重要的，怎么实现

首先明确一点 Tarjan 缩点算法一定要用到栈，管他是手写的，还是 STL 的，栈是一定会用到的

那么栈有什么用呢？

 * 用来暂时寄存跑到过得点，便于确定强连通分量。

其次了解几个数组

int  ⟹  dfn[i]  ⟹  时间戳 (第 i 个点 被第几个搜索到)

int  ⟹  low[i]  ⟹   所在强连通分量里时间戳最小的点是多少

int  ⟹  belong[i]  ⟹   点 i 在第几个强连通分量里

bool  ⟹  instack[i]  ⟹   这个点是否还在栈中

node  ⟹  edge[i].to & edge[i].nxt  ⟹  常规链式前向星

#### code

具体代码打法，直接展示伪代码，有注释

```cpp
void tarjan(int u)
{
    dfn[u] = low[u] = ++cnt;//记录时间戳
    instack[u] = true;
    stc.push(u);
    // 入栈
    for (int i = head[u]; i ; i = edge[i].nxt)
    {
        int v = edge[i].to;
        if (!dfn[v]) // 如果点没遍历过，跑一遍Tarjan，更新low[u]
        {
            tarjan(v);
            low[u] = min(low[u],low[v]);
            // 越小越好，越小环越大
        }
        else if (instack[v])// 如果在栈里，即找到环了，更新low[u]
        {
            low[u] = min(low[u],low[v]);
            // 同上
        }
    }
    if (dfn[u] == low[u]) // 如果自己时间戳与所在强连通分量里时间戳最小的点是多少相等，
    //即自己就是所在强连通分量里时间戳最小的点，
    //在栈中它上面的所用点都是他所在强连通分量的元素
    {
        int tmp;
        ++cnt;
        do//do_while保证一定操作一次
        {
            tmp = stc.top();
            stc.pop();
            belong[tmp] = cnt;
            instack[tmp] = false;
        } while (tmp != u);
    }
}

// …… 省略一部分内容

for (int i = 1; i <= n; ++i)
{
    if (!dfn[i]) // 图可能会分成多个部分部分
    {
            tarjan(i);
    }
}
```
