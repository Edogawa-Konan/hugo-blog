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

## 130. Surrounded Regions「DFS」

[Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)

>简单描述为，将不在边上的O填成X，类似于围棋。与边界O相邻的如果也是O，也不能被替换。

可以对“棋盘”的边上的每个`O`开始DFS，填充相邻的`O`为`1`，然后将整个矩阵所有的`O`填成`X`，再把`1`还原成`O`。

```java
public void solve(char[][] board) {
        if(board.length == 0)
            return;
        int m = board.length, n = board[0].length;
        for (int i = 0; i<m; i++)
        {
            if(board[i][0] == 'O')
                DFS(board, i, 0);
            if(board[i][n-1] == 'O')
                DFS(board, i, n-1);
        }
        for(int j = 0;j <n;j++)
        {
            if(board[0][j]=='O')
                DFS(board, 0, j);
            if(board[m-1][j]=='O')
                DFS(board, m-1, j);
        }
        for (int i = 0; i<m; i++)
        {
            for(int j = 0;j<n; j++)
            {
                if(board[i][j]=='O')
                    board[i][j] = 'X';
            }
        }
        for (int i = 0; i<m; i++)
        {
            for(int j = 0;j<n; j++)
            {
                if(board[i][j]=='1')
                    board[i][j] = 'O';
            }
        }
    }

    void DFS(char[][] board, int i, int j) {
        if (board[i][j] == 'O')
            board[i][j] = '1';
        if (i + 1 < board.length && board[i + 1][j] == 'O')
            DFS(board, i + 1, j);
        if (i - 1 >= 0 && board[i - 1][j] == 'O')
            DFS(board, i - 1, j);
        if (j + 1 < board[0].length && board[i][j + 1] == 'O')
            DFS(board, i, j + 1);
        if (j - 1 >= 0 && board[i][j - 1] == 'O')
            DFS(board, i, j - 1);
    }
```



## 131. Palindrome Partitioning「回溯」

[Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)

>Given a string *s*, partition *s* such that every substring of the partition is a palindrome.
>
>Return all possible palindrome partitioning of *s*.

采用求子集相同的代码思路，只不过把回文考虑进去。

```java
public List<List<String>> partition(String s) {
        List<String> path = new ArrayList<>();
        List<List<String>> res = new ArrayList<>();
        DFS(s, 0, path, res);
        return res;
    }

    void DFS(String s , int pos, List<String> path, List<List<String>> res)
    {
        if(pos==s.length())
        {
            res.add(new ArrayList<>(path));
        }
        for(int i = pos; i<s.length(); i++)
        {
            if(isPalindrome(s, pos, i))
            {
                path.add(s.substring(pos, i+1));
                DFS(s, i+1, path, res);
                path.remove(path.size() -1 );
            }
        }
    }

    public boolean isPalindrome(String s, int start, int end)
    {
        while (start < end)
            if (s.charAt(start++) != s.charAt(end--))
                return false;
        return true;
    }
```

