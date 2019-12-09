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



