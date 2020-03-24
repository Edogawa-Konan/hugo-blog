---
title: "LeetCode动态规划"
date: 2020-03-17T21:53:37+08:00
lastmod: 2020-03-17T21:53:37+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# LeetCode 动态规划

## 120. Triangle「DP」

[Triangle](https://leetcode.com/problems/triangle/)

>Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.
>
>For example, given the following triangle
>
>[
>     [2],
>    [3,4],
>   [6,5,7],
>  [4,1,8,3]
>]
>
>The minimum path sum from top to bottom is `11` (i.e., **2** + **3** + **5** + **1** = 11).

因为每个分支都会与相邻的分支重叠，同时也满足最优子结构。考虑使用DP。显然，DP可以采用两种策略，自上而下和自下而上。对于这个数组的题目而言，显然自下而上更为简单。

对于第$k$层的第$i$个结点而言，有递推关系如下：
$$
minpath[k][i] = min( minpath[k+1][i], minpath[k+1][i+1]) + triangle[k][i];
$$
实际中，因为每次计算只和下面一层的结果有关，可以仅仅使用一个一维数组保存。

```java
 public int minimumTotal(List<List<Integer>> triangle) {
        List<Integer> minpath = new ArrayList<>(triangle.get(triangle.size()-1));
        int level = triangle.size();
        for (int i = level-2; i>=0; i--)
        {
            for (int j = 0; j<=i+1; j++)
            {
                minpath.set(j, Math.min(minpath.get(j), minpath.get(j+1)) + triangle.get(i).get(j));
            }
        }
        return minpath.get(0);
    }
```



