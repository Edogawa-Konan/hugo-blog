---
title: "LeetCode数组&链表"
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

## 83. Remove Duplicates from Sorted List「两个指针」

[Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个有序链表，去除重复元素。
>
> **Example 1:**
>
> Input: 1->1->2
> Output: 1->2

```java
public ListNode deleteDuplicates(ListNode head) {
        if(head == null)
            return null;
        ListNode res = new ListNode(0);
        res.next = head;
        ListNode cur = head, pre = res;
        while (cur != null)
        {
            while (cur.next != null && cur.val == cur.next.val)
            {
                cur = cur.next;
            }
            if(pre.next != cur)
            {// no duplicates
                pre.next = cur;
            }
            pre = pre.next;
            cur = cur.next;
        }
        return res.next;
    }
```

## 82. Remove Duplicates from Sorted List II「两个指针」

[Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

>给定一个有序链表，去除重复元素，要求不保留。
>
>**Example:**
>
>Input: 1->1->1->2->3
>Output: 2->3

```java
public ListNode deleteDuplicates(ListNode head) {
        if(head == null)
            return head;
        ListNode res = new ListNode(0);
        res.next = head;
        ListNode pre = res, cur = res.next;
        while (cur!=null)
        {
            while (cur.next!=null && cur.val == cur.next.val)
            {
                cur = cur.next;
            }
            if (pre.next == cur)
            {
                pre = pre.next;
                cur = cur.next;
            }
            else
            { // duplicate detected
                pre.next = cur.next;
                cur = pre.next;
            }
        }
        return res.next;
    }
```

