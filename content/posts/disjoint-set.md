---
title: "并查集"
date: 2022-12-17T23:08:59+08:00
draft: false
categories:
- 数据结构与算法
tags:
- 并查集
summary: 对并查集的简要介绍，及两个例题
---
#### 算法
并查集主要解决的问题是，如何高效地维持和查询若干不相交集合（Disjoint sets）。该数据结构允许的操作有二：
1. 查询某个元素所属的集合，也能根据此判断两个元素是否属于同一个集合；
2. 合并两个集合；
   
并查集的思想是：将每个集合的元素组织成一棵树，并用这棵树的树根作为整个集合的代表元素，查询某个元素所属的集合时，只需返回该元素所在树的树根即可；而合并两个集合时，只需将一棵树的树根的父节点设置为另一棵树的树根。
根据上面的描述，可以看出对于树中的每个节点，我们需要的信息仅有其父节点的id（对于根节点，其父节点为其自身，这也是我们区分根节点与非根节点的方式）；若全集中的节点数已知，我们可以直接通过一个固定大小的数组保存这棵树，若需要动态的添加节点，使用哈希表替代数组即可；初始化时，将所有节点的根节点设置为他自己，即每个元素自己是一个集合，再根据题目要求逐步合并集合或响应查询。
除了上述的基本思想，实际使用的并查集往往还伴随一些可选的优化和拓展，如：
1. 路径压缩：在查询到某个节点对应的根节点后，直接将其父节点设置为该根节点，这样就压缩了该节点到根节点的路径，有利于后续查询；
2. 按秩合并：若合并集合的顺序比较巧，并查集的数据结构可能会形成近似一条直线，这就会导致某些查询的复杂度近乎$O(n)$，我们可以维持根节点的秩（rank，理解为树的层数），合并时总是让秩低的根节点成为秩高的根节点的子节点，并维持节点的秩，这就可以得到比较平衡的二叉树，从而避免上述情况；
3. 维持集合大小：我们可以在合并集合时同时维持根节点代表的集合的大小，以应对某些场景的需求；

实践中，1. 一般是必须的，因为没有额外的空间需求，编码也非常容易，2.3.则可根据具体题目添加。完整的示例代码如下：
{{<codeblock "" "cpp">}}
const int N = 1000;
int pa[N];  // Store the parent of every node
int sz[N];  // Maintain the size of every tree
int rk[N];  // Maintain the rank of every root, for a balanced tree

void init() {
    for(int i = 0; i < N; i++) pa[i] = i, sz[i] = 1, rk[i] = 0;
}

int find(int x) {
    return pa[x] == x ? x : pa[x] = find(pa[x]);
}

void merge(int x, int y) {
    int rootx = find(x), rooty = find(y);
    if(rootx != rooty) {
        if(rk[rootx] < rk[rooty]) {
            pa[rootx] = rooty;
            sz[rooty] += sz[rootx];
        } else {
            pa[rooty] = rootx;
            sz[rootx] += sz[rooty];
            if(rk[rootx] == rk[rooty]) rk[rootx] += 1;
        }
    }
}
{{</codeblock>}}
#### 例题
[LeetCode-2503](https://leetcode.cn/problems/maximum-number-of-points-from-grid-queries/description/) 

[LeetCode-1697](https://leetcode.cn/problems/checking-existence-of-edge-length-limited-paths/) 

这两道题虽然出题的形式不同，但是其解题思路却几乎一模一样，核心思想在于：按照某种顺序渐进地构造和查询并查集，使得构造的开销能够在多个查询之间均摊：
1. 对边按某个标准排序；
2. 对查询进行arg-sort；
3. 按照排序后的顺序遍历每个查询，根据其限制合并可以合并的集合，并把查询结果以源顺序填入答案数组中；
