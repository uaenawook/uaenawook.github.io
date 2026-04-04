---
title: "构建堆AdjustDownAndAdjustUp" # 标题
date: 2026-04-03T00:01:33+08:00 # 发布时间
lastmod: 2026-04-03T00:01:33+08:00 # 修改时间，为了seo搜索，告诉google你修改了文章 
author: "uaena" #作者
categories: ["代码"] # ["分类","分类2"]
tags: ["二叉树","Heap","Adjust"] # ["标签","标签2"]
description: "摘要"
draft: false # 草稿状态关闭=flase，开启为true
ShowToc: true # true=开启目录
TocOpen: false # false=目录展开
# 分类	标签
# 代码	C++, Python, 二叉树, LeetCode
# 折腾	Hugo, Git, 代理, Gemini
# 投资	A股, 美股, 加密货币, ETF
# 英语	口语, 词汇, 阅读
# 生活	CF, 洛克王国, 时间管理, 复盘
---

### 二叉树-构建堆AdjustDownAndAdjustUp
创建小根堆、大根堆时通常插入数据和删除数据，之后要调整数据，使二叉树保持为小根堆或者大根堆

这次学习的二叉树小根堆构建，是用数组构建和控制的，其余部分不是很难，就插入和删除比较不熟练，所以把这块单独整理出来了，下面是解释和代码

### AdjustUp
插入通常插入在数组的最后面，再向上调整，使数组保持为小根堆或者大根堆

#### 图解

![AdjustUp.png](./AdjustUp.png)

#### 代码

 ```c
//重写的第二遍
void AdjustUp1(HPDataType* a, int child)
{
        assert(a);

        int parent = (child - 1) / 2;
        while (child > 0)
        {
                if (a[child] < a[parent])
                {
                        swap(&a[child], &a[parent]);
                        child = parent;
                        parent = (child - 1) / 2;
                }
                else
                {
                        break;
                }
        }
}
 ```

### AdjustDown

删除通常是删除根节点，这样做是为了排序做准备，比如小根堆，每次pop根节点，根节点就是整棵树最小的值，那么删除根节点后，调整完，根还是最小的值。  

删除根节点，首先要把根和尾节点交换，size--;然后再把根节点往小的孩子那边调整，因为要让根最小，所以选择左右两边较小的孩子。  

为什么交换，因为如果直接把根删掉，那么后面所有的数据都要挪动，因为这是数组，首先消耗巨大，其次整个关系就全乱了，原本是兄弟的节点，也会变成父子关系，相当于把所有节点重新调整一遍。所以先用最后一个节点占位，然后再调整可以保证不挪动整体数据，还能保持树的结构


#### 图解

![AdjustDown.png](./AdjustDown.png)

#### 代码

```c
void AdjustDown2(HPDataType* a, int size, int parent)
{
        //假设左孩子小
        int child = parent * 2 + 1;
        //如果孩子<size则说明,还能继续调整
        while (child < size)
        {
                if (child + 1 < size && a[child+1] < a[child])
                {
                        child++;
                }
                //如果父亲大于孩子则交换，因为是小根堆，让小的当父亲
                if (a[parent] > a[child])
                {
                        swap(&a[parent], &a[child]);
                        parent = child;
                        child = parent * 2 + 1;
                }
                else
                {
                        //此时父亲比孩子小，则不用调整
                        break;
                }
        }
}
```
