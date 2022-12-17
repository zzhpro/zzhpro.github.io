---
title: "KMP算法"
date: 2022-12-18T00:21:30+08:00
draft: false
categories:
- 数据结构与算法
tags:
- KMP
summary: "KMP算法简介"
---

对于长度为$n$的字符串A和长度为$m$的字符串B，KMP算法能以$O(n+m)$的复杂度判断A是否在B中出现，而暴力算法在面对较坏情形时的复杂度可达$O(nm)$，因此KMP算法有较大优势。但是，KMP算法以难以理解著称，就我个人经验，很多时候好不容易看懂学会了，过了几天没有学习又会忘掉。目前看过的较为清晰的讲解来自于[OI-wiki](https://oi-wiki.org/string/kmp/)。本文也将基于这篇博客展开。

首先，我们定义几个概念：
* 真前缀：字符串的前缀，但不等于字符串本身；
* 真后缀：字符串的后缀，但不等于字符串本身；
* 前缀函数：
	* 对于一个长度为$n$的字符串$S$，若其子串$S[0...i]$有且仅有一对长度相等的真前缀和真后缀，设二者的长度为$k$，那么$\pi[i] = k$;
	* 若其子串$S[0...i]$有多对长度相等的真前缀和真后缀，则$\pi[i]$为这些对长度的最大值；
	* 若没有，则$\pi[i]=0$;

现在我们把字符串匹配问题分为两部分：
1. 给定字符串S，求出其前缀函数；
2. 利用前缀函数，加速S和T的匹配；

#### 求前缀函数
利用类似动态规划的思想， 利用已求出的前缀函数值求未知的前缀函数值；边界条件$\pi[0] = 0$是显然的，因为长度为一的字符串没有真前缀。
下面我们假设，$[0,i )$的前缀函数值已经求得，讨论如何求出$\pi[i]$的值。设$\pi[i-1] = j$，若$S[j] == S[i]$，则显然有$\pi[i] = j + 1$，可参考下图。
![p2](https://github.com/zzhpro/zzhpro.github.io/raw/main/static/images/p2.png)
若上述等式不成立，我们则需退而求其次，寻找子串$S[0...i-1]$的下一对相等的真前缀与真后缀，而这一对的长度就是$\pi[j - 1]$，示意图如下：
![p3](https://github.com/zzhpro/zzhpro.github.io/raw/main/static/images/p3.png)
绿色线段与蓝色线段相等，是因为我们在找相等的真前缀和真后缀；绿色线段和红色线段相等，是因为二者都是上一轮中的先等的真前缀/真后缀的后缀，因此红蓝线段对应的子串相等，而二者的长度就是已求出的$\pi[\pi[i-1]]=\pi[j-1]$。循环此过程。
故求前缀数组的代码为：
{{<codeblock "" "cpp">}}
void get_pi(vector<int>& pi, const string& s) {
    int n = s.length();
    for(int i = 1; i < n; i++) {
        int j = pi[i - 1];
        while(j > 0 && s[i] != s[j]) j = pi[j - 1];
        if(s[i] == s[j]) j++;
        pi[i] = j;
    }
}
{{</codeblock>}}
#### 利用前缀函数匹配
当我们求出了前缀数组后，可用类似双指针的思路开始匹配：
{{<codeblock "" "cpp">}}
bool kmp(const string& doc, const string& word) {
    int n = doc.length(), m = word.length();
    vector<int> pi(m);
    get_pi(pi, word);
    for(int i = 0, j = 0; i < n; i++) {
        while(j > 0 && doc[i] != word[j]) j = pi[j - 1];
        if(doc[i] == word[j]) {
            j++;
            if(j == m) return true;
        }
    }
    return false;
}
{{</codeblock>}}
#### 例题
* [LeetCode-1764](https://leetcode.cn/problems/form-array-by-concatenating-subarrays-of-another-array/) 多次调用KMP加快匹配，但实际上未必比暴力+双指针快；
* [LeetCode-28](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/description/) KMP模板题
* [LeetCode-1668](https://leetcode.cn/problems/maximum-repeating-substring/) KMP + DP
