---
title: "LeetCode回溯&搜索"
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

## 93. Restore IP Addresses[DFS]

[Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)

>给定一个数字的字符串，返回其可能代表的所有IP地址。

注意，这里使用DFS的时候不是先push再pop的形式，但是本质上是一样的。都是不断尝试的过程。

```java
public List<String> restoreIpAddresses(String s) {
        List<String> res = new ArrayList<>();
        DFS(res, s, 0, "", 0);
        return res;
    }

    private void DFS(List<String> res, String raw, int index, String tmp, int count) {
        if (count > 4)
            return;
        if (count == 4 && index == raw.length()) {
            res.add(tmp);
        }
        for (int i = 1; i <= 3; i++) {
            if (index + i > raw.length())
                break;
            String s = raw.substring(index, index + i);
            if (s.startsWith("0") && s.length() > 1 || s.length() == 3 && Integer.parseInt(s) > 255)
                continue;
            DFS(res, raw, index + i, tmp + s + (count == 3 ? "" : "."), count + 1);
        }
    }

```

