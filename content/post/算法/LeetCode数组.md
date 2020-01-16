---
title: "LeetCode数组"
date: 2020-01-16T10:13:37+08:00
lastmod: 2020-01-16T10:13:37+08:00
draft: false
tags: ['LeetCode']
categories: ['算法']
---

# LeetCode数组

## 26. Remove Duplicates from Sorted Array

[Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

> 从小到大的有序数组，要求在$O(1)$空间内，去除重复元素

## 80. Remove Duplicates from Sorted Array II

[Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

>从小到大的有序数组，要求在$O(1)$空间内，去除重复元素，每个元素最多出现2次。

以上两道题类似的结构，解法也一样，用两个指针即可。

```java
public int removeDuplicates(int[] nums) {
    int i = 0;
    for (int n : nums)
        if (i < 2 || n > nums[i-2])
            nums[i++] = n;
    return i;
}
```

