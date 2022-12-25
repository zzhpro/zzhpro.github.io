---
title: "Binary Search"
date: 2022-12-25T21:37:00+08:00
draft: false
categories:
- 数据结构与算法
tags:
- 二分查找
summary: "从invariance和progress的角度分析二分查找"
---

> Although the basic idea of binary search is comparatively straightforward, the details can be surprisingly tricky
> --Donald Knuth

计科学生一般会在他们的第一节专业基础课上学习二分查找算法———与冒泡排序、递归等差不多同期。但在面临算法题中更复杂的场景时，我往往会不知所措：循环条件该小于还是小于等于？`left=mid`还是`left=mid+1`?稍有不慎，就会使查找的结果出现错误或死循环。


我在这里构造一个一般化的场景，给出一个二分查找程序模板；并从Invariance和Progress的角度评估其正确性。

设我们有一个函数`bool feasible(int x);`，且存在一个未知的`ans`使得，`feasible(x) == (x <= ans)`恒成立，即`x<=ans`时函数的返回值为`true`，否则则为`false`。下面的代码用于求解`ans`：
{{<codeblock "" "cpp">}}
int left = some feasible value;
int right = some infeasible value;
while(left < right>) {
    int mid = left + ((right - left + 1) >> 1);
    if(feasible(mid)) {
        left = mid;
    } else {
        right = mid - 1;
    }
}
return left;
{{</codeblock>}}
#### 通过progress论证不会出现死循环
这一节中，我们暂时不考虑程序返回结果正确与否，只分析这段代码是否可能出现死循环。
由于`if-else`语句的存在，在循环体被执行一次之后，`left`和`right`中的一个会被重新赋值：
1. 若`left`被赋值：进入循环说明`left<right`，即`right - left + 1 >= 2`,那么一定有`mid > left`,程序make progress;
2. 若`right`被赋值：`mid =  left + ((right - left + 1) >> 1) <= left + ((right - left + right - left) >> 1) = right`, 那么`mid - 1 < right`，程序make progress;
综上，每次循环体都会把`left`和`right`之间的差距缩小至少1，程序不可能进入死循环。

另外，`mid`的看起来比较迷惑，最直白的写法应该是：`mid = (left + right) / 2`,我们做出的改变主要有3个：
1. `left + right`有溢出风险，改为`mid = left + (right - left) / 2`;
2. 移位比除法快，改为`mid = left + ((right - left) >> 1)`;
3. 确保上述情况1程序make progress，改为`mid = left + ((right - left + 1) >> 1)`;

#### 通过invariance证明结果的正确性
在循环中，我们维持了两个invariance：
1. `feasible(left)==true`始终成立：`left`被初始化为某个可行值，且仅当`feasible(mid)==true`时，`left`的值才会发生改变；
2. `feasible(right)==false` 或 `right == ans` 二者有且仅有一个成立：`right`被初始化为不可行的值，且仅当`feasible(mid) == false`时，`right`的值才会改编为`mid-1`，而`mid-1`的取值仅有两种情况：`feasible(mid-1) == false`或`feasible(mid-1) == true`，而对于后一种情况，`mid-1 == ans`;

综上，当且仅当`left == ans && right == ans`时循环才会结束，此时返回的结果满足“最后一个使得`feasible(x)==true`的元素”。

在初始化`left`和`right`时，我们假设了使得`feasible(x)`取真或假的`x`都存在，但并非总能如此。这里讨论两种极端情况：
1. 全部元素都`feasible`：`left`会不断增加直至`left==right`，若`right`被初始化为可能元素中最大的，返回`left`也满足“最大`feaisble`元素的条件”；
2. 全部元素都`feasible`：`right`会不断减小直至`left==right`，此时返回的`left`仍是一个infeasible的元素，出现了问题，因此，对于无法确定是否有`feasible`元素存在的题目，需要实现检查一下。

#### 例题：
1. https://leetcode.cn/problems/minimum-limit-of-balls-in-a-bag/
2. https://leetcode.cn/problems/magnetic-force-between-two-balls/
3. https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/

#### 备注
二分查找的实现方式不止一种，面对不同的题目也应有不同的变通，这里只是通过对invariance和progress这两个性质的分析，给出实际写二分查找时的分析方法，并记录一些常见的可能问题。