---
title: "Topliked100_2"
date: 2019-12-09T14:23:47+08:00
lastmod: 2019-12-09T14:23:47+08:00
draft: false
tags: ['Leetcode']
categories: ['算法']
---

# Topliked100——下

## 208-Implement Trie (Prefix Tree)「树」

[Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

> 实现一个前缀树。

```java
public class Trie {
    static class Node {
        char val;
        boolean isword;  //判断当前结点是不是一个单词
        Node[] children;

        Node(char val) {
            this.val = val;
            isword = false;
            children = new Node[26];
        }
    }
    private Node root;
    /**
     * Initialize your data structure here.
     */
    public Trie() {
        root = new Node(' ');
    }

    /**
     * Inserts a word into the trie.
     */
    public void insert(String word) {
        Node cur = root;
        for(int i = 0; i<word.length(); i++)
        {
            if(cur.children[word.charAt(i)-'a'] == null)
            {
                cur.children[word.charAt(i)-'a'] = new Node(word.charAt(i));
            }
            cur = cur.children[word.charAt(i)-'a'];
        }
        cur.isword = true;
    }

    /**
     * Returns if the word is in the trie.
     */
    public boolean search(String word) {
        Node cur = root;
        for(int i = 0;i<word.length();i++)
        {
            if(cur.children[word.charAt(i)-'a']!=null)
                cur = cur.children[word.charAt(i)-'a'];
            else
                return false;
        }
        return cur.isword;
    }

    /**
     * Returns if there is any word in the trie that starts with the given prefix.
     */
    public boolean startsWith(String prefix) {
        Node cur = root;
        for(int i = 0;i<prefix.length();i++)
        {
            if(cur.children[prefix.charAt(i)-'a']!=null)
                cur = cur.children[prefix.charAt(i)-'a'];
            else
                return false;
        }
        return true;
    }
}
```

## 215-Kth Largest Element in an Array「heap/partition」

[Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

> 找到一个数组中第k大的数

可以直接维护一个最小堆，保证容量为k，最后取出堆顶的元素即可。另一种方法基于快排的切分，最好情况下$O(n)$的时间复杂度，最坏情况下$O(n^2)$。只需要一个额外空间。可以使用shuffle来矫正一下。

```java
public int findKthLargest1(int[] nums, int k){
        k = nums.length - k; //找第k小的
        int low = 0;
        int high = nums.length -1;
        while (low < high)
        {
            int cur = partition(nums, low, high);
            if(cur > k)
                high = cur - 1;
            else if(cur < k)
            {
                low = cur + 1;
            }
            else
                break;
        }
        return nums[k];
    }

    private int partition(int[] nums, int start, int end)
    {
        int piv = nums[end];
        int i = start - 1;
        int tmp;
        for(int j = start; j<end; j++)
        {
            if(nums[j] < piv)
            {
                i++;
                tmp = nums[i];
                nums[i] = nums[j];
                nums[j] = tmp;
            }
        }
        tmp = nums[i+1];
        nums[i+1] = nums[end];
        nums[end] = tmp;
        return i+1;
    }
```

## 236-Lowest Common Ancestor of a Binary Tree「二叉树」

[Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

>给定一个二叉树，以及两个结点，找到它们的最低公共结点。

```java
public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null)
            return null;
        else if (root == p)
            return p;
        else if (root == q)
            return q;
        else {
            TreeNode left = lowestCommonAncestor(root.left, p, q);
            TreeNode right = lowestCommonAncestor(root.right, p, q);
            if (left != null && right != null)
                return root;
            else if (left != null)
                return left;
            else return right;
        }
    }
```

## 238-Product of Array Except Self「数组」

[Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

> 给定一个数组，要求返回一个数组，每个元素等于原数组除对应元素外其它所有元素的乘积。

先用一个数组记录累积，`product[i]`记录着从`nums[0]`到`nums[i-1]`的乘积。然后再从数组右侧开始遍历，用一个元素记录累积，这样就可以把当前元素`nums[i]`空出来了。

```java
public int[] productExceptSelf(int[] nums) {
        int[] product = new int[nums.length];
        product[0] = 1;

        for(int i = 1; i<nums.length; i++)
        {
            product[i] = product[i-1] * nums[i-1];
        }
        int right = 1;
        for(int i = nums.length-1; i>=0; i--)
        {
            product[i] = product[i] * right;
            right = right * nums[i];
        }
        return product;
    }
```

## 240-Search a 2D Matrix II「查找」

[Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)

> 行，列分别有序的矩阵查找指定元素。

从右上角作为起点，先向下走，再向左走。$O(M+N)$的时间复杂度。

```java
public boolean searchMatrix(int[][] matrix, int target) {
        if(matrix.length==0)
            return false;
        int row = 0, col = matrix[0].length -1;
        while (row<matrix.length&&col>=0)
        {
            if(matrix[row][col] == target)
                return true;
            else if(matrix[row][col] > target)
                col--;
            else
                row++;
        }
        return false;
    }
```

## 279-Perfect Squares「DP」

[Perfect Squares](https://leetcode.com/problems/perfect-squares/)

>给定一个数n，找到最少完美平方数（1，4， 9， 16，……）的数量，使得它们和为n，可以重复利用。

定义$DP[i]$表示和为$i$的最少完美平方数的数量，令$j$表示任意小于$i$的完美平方数（$j^2<=i$），则有
$$
DP[i] = min(DP[i], DP[i-j^2])
$$

```java
public int numSquares(int n) {
        int[] dp = new int[n+1];
        Arrays.fill(dp, Integer.MAX_VALUE);
        if(n==0)
            return 0;
        for(int i = 1; i<=n; i++)
            for(int j = 1; j*j<=i; j++)
            {
                dp[i] = Math.min(dp[i], dp[i-j*j] + 1);
            }
        return dp[n];
    }
```

## 283-Move Zeroes

[Move Zeroes](https://leetcode.com/problems/move-zeroes/)

>讲一个数组所有非0元素移到前面，0放最后。

```java
public void moveZeroes(int[] nums) {
        int index = 0;
        int i;
        for (i = 0; i < nums.length; i++) {
            if (nums[i] != 0)
                nums[index++] = nums[i];
        }
        Arrays.fill(nums, index, nums.length, 0);
    }
```

## 287-Find the Duplicate Number

[Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

>给定一个数组，n+1个整数，每个整数在1~n之间，但是有一个元素至少重复一次。找出这个元素。

注意，题目说至少重复一次，如果没有这个条件，还可以多一个解法，即先`1~n`的和，然后用这$n+1$个数的和减去前一个就是结果。

对于本道题而已，首先可以用哈希表。还有一种方法，借鉴了寻找链表的环结点的思想：

```java
public int findDuplicate(int[] nums) {
        if(nums.length==0)
            return 0;
        int slow = nums[0], fast = nums[nums[0]];
        while (slow != fast)
        {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        fast = 0;
        while (slow != fast)
        {
            slow = nums[slow];
            fast = nums[fast];
        }
        return fast;
    }
```

## 300-Longest Increasing Subsequence[DP]

[Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

>给定一个数组，求最长递增序列的长度（不一定连续）。

方法一采用DP的思想，令`dp[i]`表示以`i`为结尾的子序列的最大长度，初始化`dp[i]=1`。于是对于所有的`0<=j<i`有`dp[i] = max(dp[j]+1, dp[i]) `。时间复杂度$O(n^2)$

```java
public int lengthOfLIS(int[] nums) {
        if(nums.length==0)
            return 0;
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        int res = 1;
        for(int i = 1; i<nums.length; i++)
        {
            for(int j = 0; j<i; j++)
            {
                if(nums[i] > nums[j])
                    dp[i] = Math.max(dp[i], dp[j]+1);
            }
            res = Math.max(dp[i], res);
        }
        return res;
    }
```

方法二用一个数组end（下文同tails），记录这个最长的序列，每次二分查找。时间复杂度$O(NlogN)$。

Each time we only do one of the two:

```
(1) if x is larger than all tails, append it, increase the size by 1
(2) if tails[i-1] < x <= tails[i], update tails[i] （保证这个序列元素尽可能小）
```

```java
public int lengthOfLIS1(int[] nums){
        int[] end = new int[nums.length];
        int size = 0;
        for(int v: nums)
        {
            int i = 0, j=size;
            while (i!=j)
            {
                int m = (i+j)/2;
                if(end[m]<v)
                    i=m+1;
                else
                    j=m;
            }
            end[i] = v;
            if(i==size) size++;
        }
        return size;
    }
```







