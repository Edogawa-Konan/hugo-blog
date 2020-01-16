---
title: "LeetCode回溯"
date: 2020-01-16T10:21:50+08:00
lastmod: 2020-01-16T10:21:50+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# LeetCode回溯

## 77. Combinations

[Combinations](https://leetcode.com/problems/combinations/)

>Given two integers *n* and *k*, return all possible combinations of *k* numbers out of 1 ... *n*.

回溯即可：

```java
public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> path = new LinkedList<>();
        DFS(ans, path, 1, n, k);
        return ans;

    }
    static public void DFS(List<List<Integer>> ans, List<Integer> path, int start, int n, int k)
    {
        if(k==0)
        {
            ans.add(new ArrayList<>(path));
            return;
        }
        for(int i = start; i<=n-k+1; i++)  // 这里，start最大值是n-(k-1)，此时到n才能满足k个数的要求
        {
            path.add(i);
            DFS(ans, path, i+1, n, k-1);
            path.remove(path.size()-1);
        }
    }
```

